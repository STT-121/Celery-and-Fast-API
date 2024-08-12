# The Definitive Guide to Celery and FastAPI

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