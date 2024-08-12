## Part 3: Deployment, Testing, and Best Practices

### Chapter 9: Containerizing FastAPI, Celery, and Redis with Docker

In this chapter, we will explore how to containerize your FastAPI application along with Celery and Redis using Docker. Containerization simplifies the deployment process and ensures that your application runs consistently across different environments.

#### Objectives

-   Set up Docker for FastAPI, Celery, and Redis
-   Create Dockerfiles for each service
-   Configure Docker Compose to orchestrate the services
-   Run and test the containerized application

#### Setting Up Docker

##### 1. Install Docker

If you haven’t already, install Docker by following the instructions on the Docker website.

##### 2. Create Dockerfiles

Create Dockerfiles for the FastAPI application and Celery worker.

###### Dockerfile for FastAPI

Create a file named `Dockerfile` in your project root:

dockerfile
```docker
# Use an official Python runtime as a parent image
FROM python:3.11-slim

# Set the working directory
WORKDIR /app

# Copy the current directory contents into the container at /app
COPY . /app

# Install any needed packages specified in requirements.txt
RUN pip install --no-cache-dir -r requirements.txt

# Change the working directory to the subdirectory fastapi-celery-project
WORKDIR /app/fastapi-celery-project

# Make port 8000 available to the world outside this container
EXPOSE 8000

# Define environment variable
ENV NAME FastAPI

# Run app.py when the container launches
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```
###### Dockerfile for Celery

Create a file named `Dockerfile.celery` in your project root:

dockerfile
```docker
# Use an official Python runtime as a parent image
FROM python:3.11-slim

# Set the working directory
WORKDIR /app

# Copy the current directory contents into the container at /app
COPY . /app

# Install any needed packages specified in requirements.txt
RUN pip install --no-cache-dir -r requirements.txt

# Change the working directory to the subdirectory fastapi-celery-project
WORKDIR /app/fastapi-celery-project

# Define environment variable
ENV NAME Celery

# Run Celery worker when the container launches
CMD ["celery", "-A", "task.celery", "worker", "--loglevel=info"]
```
##### 3. Create a Docker Compose Configuration

Docker Compose simplifies the process of running multiple containers. Create a `docker-compose.yml` file in your project root:

yaml
```yaml
version: '3.8'

services:
  fastapi:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "8000:8000"
    depends_on:
      - redis
    environment:
      - CELERY_BROKER_URL=redis://redis:6379/0
      - CELERY_RESULT_BACKEND=redis://redis:6379/0

  celery:
    build:
      context: .
      dockerfile: Dockerfile.celery
    depends_on:
      - redis
    environment:
      - CELERY_BROKER_URL=redis://redis:6379/0
      - CELERY_RESULT_BACKEND=redis://redis:6379/0

  redis:
    image: "redis:6-alpine"
    ports:
      - "6379:6379" 
```
##### 4. Build and Run the Containers

Use Docker Compose to build and run the containers:

bash
```bash
$ docker-compose up --build 
```
Docker Compose will build the images and start the containers for FastAPI, Celery, and Redis. You should see logs indicating that the services are running.

##### 5. Test the Containerized Application

Open your browser and navigate to `http://localhost:8000`. You should see your FastAPI application running.

To test Celery tasks, use a tool like `curl` or Postman to send requests to the FastAPI endpoints that trigger Celery tasks.

Example:

bash
```bash
$ curl -X POST "http://localhost:8000/tasks/add_user" -H "Content-Type: application/json" -d '{"name": "John Doe", "email": "john.doe@example.com"}
``` 

#### Best Practices

##### 1. Use Environment Variables

Manage your configuration using environment variables to ensure that your application is portable and secure. Update your `docker-compose.yml` to pass environment variables:

yaml
```yaml
environment:
  - CELERY_BROKER_URL=redis://redis:6379/0
  - CELERY_RESULT_BACKEND=redis://redis:6379/0
  - DATABASE_URL=sqlite:///./test.db 
```
Update your `config.py` to read these environment variables:

python
```python
import os
from pydantic import BaseSettings

class Settings(BaseSettings):
    APP_NAME: str = "FastAPI with Celery"
    CELERY_BROKER_URL: str = os.getenv('CELERY_BROKER_URL', 'redis://localhost:6379/0')
    CELERY_RESULT_BACKEND: str = os.getenv('CELERY_RESULT_BACKEND', 'redis://localhost:6379/0')
    DATABASE_URL: str = os.getenv('DATABASE_URL', 'sqlite:///./test.db')

    class Config:
        env_file = ".env"

def get_settings() -> Settings:
    return Settings()
```
##### 2. Use Multi-stage Builds

Optimize your Dockerfiles using multi-stage builds to reduce the size of your Docker images. Example for FastAPI:

dockerfile
```bash
# Use an official Python runtime as a parent image
FROM python:3.11-slim AS builder

# Set the working directory
WORKDIR /app

# Copy the current directory contents into the container at /app
COPY . /app

# Install any needed packages specified in requirements.txt
RUN pip install --no-cache-dir -r requirements.txt

# Use a lightweight base image
FROM python:3.11-slim

WORKDIR /app

COPY --from=builder /app /app

# Change the working directory to the subdirectory fastapi-celery-project
WORKDIR /app/fastapi-celery-project

EXPOSE 8000

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"] 
```
##### 3. Use Health Checks

Configure health checks to ensure that your services are running properly. Update `docker-compose.yml`:

yaml
```yaml
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost:8000/health-check"]
  interval: 30s
  timeout: 10s
  retries: 3 
```
##### 4. Use Volumes for Persistent Storage

Mount volumes to ensure that data persists across container restarts. Example for Redis:

yaml
```yaml
redis:
  image: "redis:6-alpine"
  ports:
    - "6379:6379"
  volumes:
    - redis_data:/data

volumes:
  redis_data: 
```
#### Conclusion

In this chapter, you have learned how to containerize your FastAPI application along with Celery and Redis using Docker. You created Dockerfiles for each service, configured Docker Compose to orchestrate the services, and tested the containerized application. By following best practices, you can ensure that your containerized application is secure, efficient, and maintainable. In the next chapter, we will cover validating Celery tasks.