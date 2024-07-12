# The Definitive Guide to Celery and FastAPI

## Part 1: Getting Started

### Chapter 1: Introduction

Welcome to "The Definitive Guide to Celery and FastAPI." This course is designed to help you integrate Celery with FastAPI to handle asynchronous tasks efficiently. Whether you are developing a simple web application or a complex system, using Celery can significantly enhance your application's performance by offloading time-consuming tasks to the background.

#### What is Celery?

Celery is an open-source, distributed task queue framework written in Python. It allows you to run long-running or scheduled tasks outside of your main application process. Celery is flexible and can be integrated with various web frameworks and message brokers.

Key features of Celery:

-   **Asynchronous Task Execution**: Run tasks in the background without blocking the main application process.
-   **Scheduled Tasks**: Schedule tasks to run at specific intervals or times.
-   **Retry Mechanism**: Automatically retry failed tasks.
-   **Task Monitoring**: Monitor task execution using tools like Flower.
-   **Distributed Execution**: Run tasks across multiple workers and machines.

#### What is FastAPI?

FastAPI is a modern, high-performance web framework for building APIs with Python. It is based on standard Python type hints and provides automatic interactive API documentation. FastAPI is known for its speed and ease of use.

Key features of FastAPI:

-   **High Performance**: Asynchronous capabilities provide superior performance, comparable to Node.js and Go.
-   **Automatic Documentation**: Generate interactive API documentation with Swagger UI and ReDoc.
-   **Type Safety**: Leverage Python type hints for input validation and better code quality.
-   **Dependency Injection**: Simplify code with built-in dependency injection.

#### Why Combine Celery and FastAPI?

Combining Celery and FastAPI allows you to build highly responsive and scalable applications. Here are a few scenarios where this combination is particularly useful:

-   **Running Machine Learning Models**: Offload the computation to the background, allowing your API to respond quickly.
-   **Sending Bulk Emails**: Send emails asynchronously to avoid blocking the main application process.
-   **Processing Images or PDFs**: Handle image or PDF processing tasks in the background.
-   **Generating Reports**: Generate and export user data without affecting the application's responsiveness.
-   **Performing Backups**: Schedule regular backups without manual intervention.

#### Course Structure

This course is divided into four parts:

1.  **Getting Started**: Set up your environment and build a basic FastAPI application with Celery.
2.  **Foundation and Concepts**: Dive into the core concepts of Celery and learn how to manage tasks, handle errors, and integrate with SQLAlchemy.
3.  **Deployment, Testing, and Best Practices**: Learn how to containerize your application, validate tasks, monitor with Flower, and handle real-time notifications.
4.  **Advanced Topics**: Explore scaling, optimizing task performance, securing tasks, and real-world case studies.

By the end of this course, you will have a solid understanding of how to use Celery with FastAPI to build robust and scalable applications.

Let's get started! In the next chapter, we will cover the prerequisites and set up your development environment.


### Chapter 2: Prerequisites

Before diving into integrating Celery with FastAPI, it’s important to set up your development environment with all the necessary tools and dependencies. This chapter will guide you through the prerequisites and initial setup.

#### Prerequisites

1.  **Python 3.7+**
    
    -   Make sure you have Python 3.7 or later installed on your system. You can download it from the official Python website.
2.  **Basic Knowledge of FastAPI**
    
    -   Familiarity with FastAPI basics is assumed. If you are new to FastAPI, consider going through the FastAPI documentation to get up to speed.
3.  **Docker**
    
    -   Docker is highly recommended for setting up Redis and other dependencies. You can download and install Docker from the official website.
4.  **Redis**
    
    -   Redis will be used as the message broker and result backend for Celery tasks. It can be run using Docker, which we will cover shortly.
5.  **Virtual Environment**
    
    -   It’s good practice to use a virtual environment to manage your project’s dependencies. You can use `venv`, `virtualenv`, or other tools like `Poetry` or `Pipenv`.

#### Setting Up the Development Environment

1.  **Create a Project Directory**
    
    ```bash
    $ mkdir fastapi-celery-project && cd fastapi-celery-project
    ``` 
    
2.  **Set Up a Virtual Environment**
    
    Create and activate a virtual environment:
    
    ```bash
    $ python -m venv venv
    $ source venv/bin/activate
    (venv)$ 
    ```
    You should now see `(venv)` at the beginning of your terminal prompt, indicating that the virtual environment is active.
    
3.  **Create a `requirements.txt` File**
    
    Create a `requirements.txt` file in your project directory with the following content:

     ```python
     fastapi==0.111.0
     uvicorn==0.30.1
     celery==5.4.0
     redis==5.0.7
     ```
    
4.  **Install Dependencies**
    
    Install the dependencies listed in `requirements.txt`:
       
    
    ```bash
    (venv)$ pip install -r requirements.txt
    ``` 
    
5.  **Set Up Redis with Docker**
    
    If you haven’t installed Docker, follow the instructions on the Docker website to install it.
    
    Once Docker is installed, run the following command to start a Redis container:
    
    ```docker
    $ docker run -p 6379:6379 --name some-redis -d redis
    ```
    
    This command downloads the official Redis Docker image from Docker Hub and runs it on port 6379 in the background.
    
    To test if Redis is up and running, run:
    
    bash

    `$ docker exec -it some-redis redis-cli ping` 
    
    You should see:
    
    text
    
    
    `PONG` 
    
6.  **Initial FastAPI Application**
    
    Create a new file called `main.py` and add the following code to set up a basic FastAPI application:
    
    python
    ```python
    from fastapi import FastAPI

    app = FastAPI()

    @app.get("/")
    async def root():
    return {"message": "Hello World"}
    ```
    
7.  **Run the FastAPI Application**
    
    Start the FastAPI application using Uvicorn:
    
    bash   
    `(venv)$ uvicorn main:app --reload` 
    
    You should see the following output:
    ```python
    INFO:     Uvicorn running on http://127.0.0.1:8000 (Press CTRL+C to quit)
    INFO:     Started reloader process [66193] using WatchFiles
    INFO:     Started server process [66399]
    INFO:     Waiting for application startup.
    INFO:     Application startup complete.
    ```
    Visit `http://localhost:8000` in your browser. You should see `{"message":"Hello World"}`.
    
    

#### Conclusion

Now that you have your development environment set up, you are ready to start integrating Celery with your FastAPI application. In the next chapter, we will create a basic FastAPI project and set up Celery to handle background tasks.

### Chapter 3: Getting Started

In this chapter, we will set up a basic FastAPI project and configure Celery to handle background tasks. We'll start by creating a new FastAPI project and then integrate Celery to manage asynchronous tasks. Additionally, we'll cover how to execute Celery tasks in the Python shell and monitor them using Flower.

#### Objectives

-   Set up Celery with FastAPI
-   Execute Celery tasks in the Python shell
-   Monitor a Celery app with Flower

### Setting up Redis

You can set up and run Redis directly from your operating system or from a Docker container. Using Docker is recommended since it simplifies dependency management and setup.

#### With Docker

Start by installing Docker if you haven't already. Then, open your terminal and run the following command:

bash

```docker
$ docker run -p 6379:6379 --name some-redis -d redis
``` 

This command downloads the official Redis Docker image from Docker Hub and runs it on port 6379 in the background.

To test if Redis is up and running, run:

bash

`$ docker exec -it some-redis redis-cli ping` 

You should see:

`PONG` 

#### Without Docker

Download Redis from the source or via a package manager (like APT, YUM, Homebrew, or Chocolatey) and then start the Redis server via:

bash

`$ redis-server` 

To test if Redis is up and running, run:

bash

`$ redis-cli ping` 

You should see:

text

`PONG` 

### Setting up Celery

#### Create a FastAPI project

Create a new project directory:

bash

`$ mkdir fastapi-celery-project && cd fastapi-celery-project` 

Create and activate a new Python virtual environment:

bash

```bash
$ python -m venv venv
$ source venv/bin/activate
(venv)$
``` 

Create a `requirements.txt` file with the following content:

```python
fastapi==0.111.0
uvicorn[standard]==0.30.1
celery==5.4.0
redis==5.0.7
``` 

Install the dependencies:

bash

`(venv)$ pip install -r requirements.txt` 

Create a new file called `main.py` and add the following code:

python

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
async def root():
    return {"message": "Hello World"}
``` 

Run the app:

bash

`(venv)$ uvicorn main:app --reload` 

You should see:

```python
INFO:     Uvicorn running on http://127.0.0.1:8000 (Press CTRL+C to quit)
INFO:     Started reloader process [66193] using WatchFiles
INFO:     Started server process [66399]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
``` 

Visit `http://localhost:8000` in your browser. You should see `{"message":"Hello World"}`. Press Ctrl+C to terminate the development server.

Your project structure should now look like this:

css


```css
├── main.py
└── requirements.txt
``` 

### Add Celery

Update `main.py` to configure Celery:

python
```python
from celery import Celery
from fastapi import FastAPI

app = FastAPI()

celery = Celery(
    "worker",
    broker="redis://127.0.0.1:6379/0",
    backend="redis://127.0.0.1:6379/0"
)

@app.get("/")
async def root():
    return {"message": "Hello World"}

@celery.task
def divide(x, y):
    import time
    time.sleep(5)
    return x / y
 ```

### Sending a Task to Celery

With the configuration done, let's try sending a task to Celery to see how it works.

In a new terminal window, navigate to your project directory, activate the virtual environment, and then run:

bash

`(venv)$ celery -A main.celery worker --loglevel=info` 

You should see something similar to:
text

```python
[config]
.> app:         main:0x10ad0d5f8
.> transport:   redis://127.0.0.1:6379/0
.> results:     redis://127.0.0.1:6379/0
.> concurrency: 8 (prefork)
.> task events: OFF (enable -E to monitor tasks in this worker)

[queues]
.> celery           exchange=celery(direct) key=celery

[tasks]
  . main.divide
``` 

Back in the first terminal window, run:

bash

`(venv)$ python` 

Let's send some tasks to the Celery worker:

python


```python
>>> from main import app, divide
>>> task = divide.delay(1, 2)
``` 

You should see in the Celery worker terminal:

text

```python
[2024-01-04 15:40:53,959: INFO/MainProcess] Task main.divide[3d5b4872-2fa4-4e08-b916-aadf59f54271] received
[2024-01-04 15:40:58,978: INFO/ForkPoolWorker-16] Task main.divide[3d5b4872-2fa4-4e08-b916-aadf59f54271] succeeded in 5.0168835959921125s: 0.5
``` 

Add another task or two. As you do this, picture the workflow in your head:

-   The Celery client (the producer) adds a new task to the queue via the message broker.
-   The Celery worker (the consumer) grabs the tasks from the queue, again via the message broker.
-   Once processed, results are stored in the result backend.

Add another new task:

python

```python
>>> task = divide.delay(1, 2)
``` 

Check the task state and result:

python

```python
>>> type(task)
<class 'celery.result.AsyncResult'>

>>> print(task.state, task.result)
PENDING None

>>> print(task.state, task.result)
PENDING None

>>> print(task.state, task.result)
SUCCESS 0.5

>>> print(task.state, task.result)
SUCCESS 0.5
```
What happens if there's an error?

python

````python
>>> task = divide.delay(1, 0)

# wait a few seconds before checking the state and result

>>> task.state
'FAILURE'

>>> task.result
ZeroDivisionError('division by zero')
````

### Monitoring Celery with Flower

Flower is a real-time web application monitoring and administration tool for Celery.

Add the dependency to the `requirements.txt` file:

text

`flower==2.0.1` 

Open a third terminal window, navigate to the project directory, activate your virtual environment, and then install Flower:

bash

`(venv)$ pip install -r requirements.txt` 

Once installed, spin up the server:

bash

`(venv)$ celery -A main.celery flower --port=5555` 

Navigate to `http://localhost:5555` in your browser to view the dashboard. Click "Tasks" in the nav bar at the top to view the finished tasks.

In the first terminal window, run a few more tasks, making sure you have at least one that will fail:

python

```python
>>> task = divide.delay(1, 2)
>>> task = divide.delay(1, 0)
>>> task = divide.delay(1, 2)
>>> task = divide.delay(1, 3)
``` 

Back in Flower, you should see the tasks listed. Take note of the UUID column. This is the ID of AsyncResult. Copy the UUID for the failed task and open the terminal window where the FastAPI shell is running to view the details:

python

```python
>>> from celery.result import AsyncResult
>>> task = AsyncResult('8e3da1cc-a6aa-42ba-ab72-6ca7544d3730')  # replace with your UUID

>>> task.state
'FAILURE'

>>> task.result
ZeroDivisionError('division by zero')
``` 

Familiarize yourself with the Flower dashboard. It's a powerful tool that can help make it easier to learn Celery since you can get feedback much quicker than from the terminal.


### Chapter 4: Application Factory

In this chapter, we will focus on the application factory pattern for structuring your FastAPI application. This pattern is particularly useful for large applications as it promotes modularity and flexibility. By using an application factory, you can create multiple instances of your application with different configurations.

### What is an Application Factory?

An application factory is a function that returns a new instance of your application. This approach allows you to configure the application dynamically, which is especially useful for testing or creating different environments (development, testing, production).

#### Benefits of Using an Application Factory:

-   **Modularity**: Break down your application into reusable components.
-   **Flexibility**: Easily create different configurations for various environments.
-   **Testability**: Simplify the process of testing by creating isolated application instances.

### Creating an Application Factory

To create an application factory in FastAPI, you need to define a function that initializes and returns a FastAPI application instance. Below is an example structure:

#### 1. Define the Factory Function

Create a file named `app_factory.py` and define your factory function:

python

```python
from fastapi import FastAPI
from celery import Celery
from .config import get_settings
from .routers import api_router

def create_app() -> FastAPI:
    app = FastAPI()

    # Load configuration
    settings = get_settings()
    app.config.update(settings.dict())

    # Register routers
    app.include_router(api_router)

    # Initialize Celery
    celery = Celery(
        app.import_name,
        backend=settings.CELERY_RESULT_BACKEND,
        broker=settings.CELERY_BROKER_URL,
    )
    celery.conf.update(app.config)
    app.celery = celery

    return app
``` 

#### 2. Configuration Settings

Create a configuration file `config.py` to manage your settings:

python

```python
from pydantic import BaseSettings

class Settings(BaseSettings):
    APP_NAME: str = "FastAPI with Celery"
    CELERY_BROKER_URL: str = "redis://localhost:6379/0"
    CELERY_RESULT_BACKEND: str = "redis://localhost:6379/0"

    class Config:
        env_file = ".env"

def get_settings() -> Settings:
    return Settings()
```
#### 3. Registering Routers

Organize your routes in a separate file `routers.py`:

python

```python
from fastapi import APIRouter

api_router = APIRouter()

@api_router.get("/health-check")
def health_check():
    return {"status": "ok"}
```

#### 4. Initializing the Application

In your main file `main.py`, initialize the application using the factory function:

python

```python
from app_factory import create_app

app = create_app()
``` 

#### 5. Running the Application

Run your FastAPI application using the `uvicorn` command:

bash

`uvicorn main:app --reload` 

### Conclusion

By using an application factory, you can efficiently manage your FastAPI application configuration and initialization. This approach not only improves the modularity and flexibility of your application but also enhances its testability. In the next chapters, we will dive deeper into integrating Celery tasks and managing them effectively within this structure.
