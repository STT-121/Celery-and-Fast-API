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
from config import get_settings
from routers import api_router

def create_app() -> FastAPI:
    app = FastAPI()

    # Load configuration
    settings = get_settings()

    # Register routers
    app.include_router(api_router)

    # Initialize Celery
    celery = Celery(
        __name__,
        backend=settings.CELERY_RESULT_BACKEND,
        broker=settings.CELERY_BROKER_URL,
    )

    # Convert settings to dict and remove duplicates
    celery_settings = settings.dict()
    celery_settings.pop('CELERY_RESULT_BACKEND', None)
    celery_settings.pop('CELERY_BROKER_URL', None)
    celery.conf.update(celery_settings)

    # Attach settings to the app (if needed)
    app.settings = settings
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

## Part 2: Foundation and Concepts

### Chapter 5: Configuring Celery with FastAPI

In this chapter, we will configure Celery to work with our FastAPI application. We will set up the necessary configuration files and ensure that Celery can handle tasks properly.

### Objectives

-   Configure Celery settings
-   Integrate Celery with FastAPI
-   Create and execute Celery tasks

### Configuring Celery

#### 1. Update Configuration Settings

First, let's update our configuration settings to include Celery-specific settings. Modify `config.py` as follows:
python
``` python
from pydantic_settings import BaseSettings


class Settings(BaseSettings):
    APP_NAME: str = "FastAPI with Celery"
    CELERY_BROKER_URL: str = "redis://localhost:6379/0"
    CELERY_RESULT_BACKEND: str = "redis://localhost:6379/0"

    class Config:
        env_file = ".env"

def get_settings() -> Settings:
    return Settings()
  ```

#### 2. Create a Celery Instance

Next, let's create a Celery instance and configure it with the settings defined above. Update `app_factory.py`:

python
```python
from fastapi import FastAPI
from celery import Celery
from config import get_settings

def create_celery() -> Celery:
    celery = Celery(
        "worker",
        backend='redis://localhost:6379/0',
        broker='redis://localhost:6379/0',
    )
    return celery

def create_app() -> FastAPI:
    app = FastAPI()

    # Load configuration
    settings = get_settings()

    # Register routers
    from routers import api_router
    app.include_router(api_router)

    # # Attach settings to the app (if needed)
    app.settings = settings
    app.celery = create_celery()
    # app.CELERY_RESULT_BACKEND = 'redis://localhost:6379/0'

    return app
```

#### 3. Define Celery Tasks

Create a new file `tasks.py` to define our Celery tasks:

python
```python
from app_factory import create_celery

celery = create_celery()

@celery.task
def add(x, y):
    return x + y

@celery.task
def mul(x, y):
    return x * y

@celery.task
def xsum(numbers):
    return sum(numbers)
```

#### 4. Register Task Routes

Update `routers.py` to include routes for triggering Celery tasks:

python

```python
from fastapi import APIRouter, HTTPException
from task import add, mul, xsum
from pydantic import BaseModel
from app_factory import create_app
import pdb


api_router = APIRouter()

class AddTask(BaseModel):
    x: int
    y: int

class MulTask(BaseModel):
    x: int
    y: int

class XSumTask(BaseModel):
    numbers: list[int]


@api_router.get("/health-check")
def health_check():
    return {"status": "ok"}

@api_router.post("/tasks/add")
def run_add_task(task: AddTask):
    task = add.apply_async((task.x, task.y))
    return {"task_id": task.id}

@api_router.post("/tasks/mul")
def run_mul_task(task : MulTask):
    task = mul.apply_async((task.x, task.y))
    return {"task_id": task.id}

@api_router.post("/tasks/xsum")
def run_xsum_task(task: XSumTask):
    task = xsum.apply_async((task.numbers,))
    return {"task_id": task.id}

@api_router.get("/tasks/{task_id}")
def get_task_status(task_id: str):
    app=create_app()
    task = app.celery.AsyncResult(task_id)    
    if task.state == 'PENDING':
        return {"task_id": task.id, "state": task.state}
    elif task.state != 'FAILURE':
        return {"task_id": task.id, "state": task.state, "result": task.result}
    else:
        return {"task_id": task.id, "state": task.state, "error": str(task.info)}
``` 

### Running the Application

To run the application, follow these steps:

1.  Start Redis using Docker (if not already running):
    
    bash
	```bash
    $ docker run -p 6379:6379 --name some-redis -d redis
	``` 
    
2.  Start the FastAPI application:
    
    bash
    
	```bash
    (venv)$ uvicorn main:app --reload
	```
    
3.  In a new terminal, start the Celery worker:
    
    bash
	```bash
    (venv)$ celery -A task.celery worker --loglevel=info
	```
### Testing Celery Tasks

You can test the Celery tasks by sending HTTP requests to the FastAPI endpoints using a tool like `curl` or Postman. For example:

1.  Add Task:
    bash
    ```bash
    $ curl -X POST "http://localhost:8000/tasks/add" -H "Content-Type: application/json" -d '{"x": 4, "y": 6}'
	   ``` 
    
2.  Multiply Task:
    bash 
	   ```bash
    $ curl -X POST "http://localhost:8000/tasks/mul" -H "Content-Type: application/json" -d '{"x": 4, "y": 6}'
	``` 
    
3.  Sum Task:
    bash  
    ```bash
    $ curl -X POST "http://localhost:8000/tasks/xsum" -H "Content-Type: application/json" -d '{"numbers": [1, 2, 3, 4, 5]}'
    ``` 
    
4.  Check Task Status:
    bash
    ```bash
    $ curl -X GET "http://localhost:8000/tasks/{task_id}"
    ``` 
    

Replace `{task_id}` with the actual task ID returned by the task creation endpoints.

### Conclusion

You have now successfully configured Celery with FastAPI and created some basic tasks. You can run and monitor these tasks to ensure they execute correctly. In the next chapter, we will dive deeper into managing Celery tasks, handling errors, and ensuring tasks work correctly with SQLAlchemy.


### Chapter 6: Task Management with Celery

In this chapter, we will explore various aspects of task management with Celery. We will cover how to create, schedule, and manage tasks effectively. Additionally, we will discuss task retries and error handling.

### Objectives

-   Create and manage Celery tasks
-   Schedule tasks
-   Handle task retries and errors
-   Ensure tasks work with SQLAlchemy

### Creating and Managing Celery Tasks

#### 1. Creating Tasks

You can define tasks in your `tasks.py` file. Here is an example of basic tasks:

python

```python
from .app_factory import create_celery

celery = create_celery()

@celery.task
def add(x, y):
    return x + y

@celery.task
def mul(x, y):
    return x * y

@celery.task
def xsum(numbers):
    return sum(numbers) 
```
#### 2. Scheduling Tasks

Celery supports periodic tasks using the `celery.beat` scheduler. To schedule tasks, you need to define a `celerybeat_schedule` in your Celery configuration.

Update `app_factory.py` to include periodic task configuration:

python
```python
from celery.schedules import crontab

def create_celery(app: FastAPI) -> Celery:
    settings = get_settings()
    celery = Celery(
        app.import_name,
        backend=settings.CELERY_RESULT_BACKEND,
        broker=settings.CELERY_BROKER_URL,
    )
    celery.conf.update(app.config)
    
    celery.conf.beat_schedule = {
        'add-every-30-seconds': {
            'task': 'app.tasks.add',
            'schedule': 30.0,
            'args': (16, 16)
        },
        'multiply-at-midnight': {
            'task': 'app.tasks.mul',
            'schedule': crontab(hour=0, minute=0),
            'args': (4, 5)
        },
    }

    return celery
```
#### 3. Running the Celery Beat Scheduler

To run the Celery beat scheduler alongside your worker, use the following command:

bash
```bash
(venv)$ celery -A task.celery worker --loglevel=info --beat
```

### Handling Task Retries and Errors

#### 1. Retrying Tasks

Celery provides a built-in mechanism for retrying tasks in case of failure. You can specify the retry parameters using the `autoretry_for`, `retry_kwargs`, and `retry_backoff` options.

Update `tasks.py` to include a retry mechanism:

python

```python
from celery import Celery
from celery.exceptions import Retry

celery = create_celery()

@celery.task(autoretry_for=(Exception,), retry_kwargs={'max_retries': 5}, retry_backoff=True)
def add(x, y):
    if y == 0:
        raise Retry("Can't divide by zero")
    return x + y

@celery.task(autoretry_for=(Exception,), retry_kwargs={'max_retries': 5}, retry_backoff=True)
def mul(x, y):
    if y == 0:
        raise Retry("Can't multiply by zero")
    return x * y

@celery.task(autoretry_for=(Exception,), retry_kwargs={'max_retries': 5}, retry_backoff=True)
def xsum(numbers):
    if not numbers:
        raise Retry("Empty list provided")
    return sum(numbers)
```
#### 2. Error Handling

You can handle errors in your tasks by catching exceptions and taking appropriate actions. For example, logging the error or sending notifications.

Update `tasks.py` to include error handling:

python
```python
import logging

celery = create_celery()

logger = logging.getLogger(__name__)

@celery.task(autoretry_for=(Exception,), retry_kwargs={'max_retries': 5}, retry_backoff=True)
def add(x, y):
    try:
        result = x + y
    except Exception as e:
        logger.error(f"Error adding {x} and {y}: {e}")
        raise
    return result

@celery.task(autoretry_for=(Exception,), retry_kwargs={'max_retries': 5}, retry_backoff=True)
def mul(x, y):
    try:
        result = x * y
    except Exception as e:
        logger.error(f"Error multiplying {x} and {y}: {e}")
        raise
    return result

@celery.task(autoretry_for=(Exception,), retry_kwargs={'max_retries': 5}, retry_backoff=True)
def xsum(numbers):
    try:
        result = sum(numbers)
    except Exception as e:
        logger.error(f"Error summing {numbers}: {e}")
        raise
    return result
```
### Ensuring Tasks Work with SQLAlchemy

When working with SQLAlchemy, it's important to ensure that tasks use the correct database session. You can use the `scoped_session` from SQLAlchemy to manage sessions effectively.

#### 1. Setting Up SQLAlchemy

First, set up SQLAlchemy in your project.
install the libray:
```bash
pip install SQLAlchemy==2.0.31
```

Create a file `database.py`:

python
```python
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

SQLALCHEMY_DATABASE_URL = "sqlite:///./test.db"

engine = create_engine(SQLALCHEMY_DATABASE_URL, connect_args={"check_same_thread": False})
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

Base = declarative_base()
```
#### 2. Using SQLAlchemy in Tasks

Update `tasks.py` to use SQLAlchemy sessions:

python
```python
from app_factory import create_celery
from celery.exceptions import Retry
import logging
from database import SessionLocal

celery = create_celery()

logger = logging.getLogger(__name__)
@celery.task(autoretry_for=(Exception,), retry_kwargs={'max_retries': 5}, retry_backoff=True)
def add(x, y):
    db = SessionLocal()
    try:
        result = x + y
    except Exception as e:
        logger.error(f"Error adding {x} and {y}: {e}")
        db.rollback()
        raise
    finally:
        db.close()
    return result

@celery.task(autoretry_for=(Exception,), retry_kwargs={'max_retries': 5}, retry_backoff=True)
def mul(x, y):
    db = SessionLocal()
    try:
        result = x * y
    except Exception as e:
        db.rollback()
        logger.error(f"Error multiplying {x} and {y}: {e}")
        raise
    finally:
        db.close()
    return result

@celery.task(autoretry_for=(Exception,), retry_kwargs={'max_retries': 5}, retry_backoff=True)
def xsum(numbers):
    db = SessionLocal()
    try:
        result = sum(numbers)
    except Exception as e:
        logger.error(f"Error summing {numbers}: {e}")
        db.rollback()
        raise
    finally:
        db.close
    return result
```
### Conclusion

In this chapter, you have learned how to create, manage, and schedule Celery tasks. You also explored how to handle task retries and errors effectively. Additionally, you integrated SQLAlchemy with Celery tasks to ensure proper database session management. In the next chapter, we will dive deeper into error handling and debugging Celery tasks.

### Chapter 7: Error Handling and Debugging

In this chapter, we will explore strategies for error handling and debugging Celery tasks. Proper error handling ensures that your application can gracefully manage failures, while effective debugging helps you identify and resolve issues quickly.

### Objectives

-   Implement robust error handling in Celery tasks
-   Use built-in Celery features for task retries and failure management
-   Debug Celery tasks using logs and monitoring tools

### Error Handling in Celery Tasks

#### 1. Using `try-except` Blocks

The simplest way to handle errors in Celery tasks is by using `try-except` blocks. This allows you to catch exceptions and take appropriate actions.

Update `tasks.py` to include detailed error handling:

python
```python
import logging
from celery import Celery
from celery.exceptions import Retry

celery = create_celery()

logger = logging.getLogger(__name__)

@celery.task(bind=True, autoretry_for=(Exception,), retry_kwargs={'max_retries': 5}, retry_backoff=True)
def add(self, x, y):
    try:
        result = x + y
    except Exception as exc:
        logger.error(f"Error adding {x} and {y}: {exc}")
        raise self.retry(exc=exc)
    return result

@celery.task(bind=True, autoretry_for=(Exception,), retry_kwargs={'max_retries': 5}, retry_backoff=True)
def mul(self, x, y):
    try:
        result = x * y
    except Exception as exc:
        logger.error(f"Error multiplying {x} and {y}: {exc}")
        raise self.retry(exc=exc)
    return result

@celery.task(bind=True, autoretry_for=(Exception,), retry_kwargs={'max_retries': 5}, retry_backoff=True)
def xsum(self, numbers):
    try:
        result = sum(numbers)
    except Exception as exc:
        logger.error(f"Error summing {numbers}: {exc}")
        raise self.retry(exc=exc)
    return result 
```
#### 2. Task States and Custom Error Messages

Celery tasks can return custom error messages and set specific task states. You can define these states and messages to provide more context about the failure.

Update `tasks.py` to return custom error messages:

python

```python
@celery.task(bind=True, autoretry_for=(Exception,), retry_kwargs={'max_retries': 5}, retry_backoff=True)
def add(self, x, y):
    try:
        result = x + y
    except Exception as exc:
        logger.error(f"Error adding {x} and {y}: {exc}")
        self.update_state(state='FAILURE', meta={'exc': str(exc), 'task': 'add'})
        raise self.retry(exc=exc)
    return result
```
#### 3. Sending Notifications on Task Failure

You can integrate notification services to alert you when a task fails. This can be done using email, Slack, or any other notification service.

Example of sending an email notification:

python
```python
import smtplib
from email.mime.text import MIMEText

@celery.task(bind=True, autoretry_for=(Exception,), retry_kwargs={'max_retries': 5}, retry_backoff=True)
def add(self, x, y):
    try:
        result = x + y
    except Exception as exc:
        logger.error(f"Error adding {x} and {y}: {exc}")
        self.update_state(state='FAILURE', meta={'exc': str(exc), 'task': 'add'})
        
        # Send email notification
        msg = MIMEText(f"Task add failed with error: {exc}")
        msg['Subject'] = 'Celery Task Failure Notification'
        msg['From'] = 'your-email@example.com'
        msg['To'] = 'admin@example.com'
        
        with smtplib.SMTP('localhost') as server:
            server.sendmail(msg['From'], [msg['To']], msg.as_string())
        
        raise self.retry(exc=exc)
    return result
```
### Debugging Celery Tasks

#### 1. Using Logs

Logging is a crucial aspect of debugging. Ensure that you have proper logging configured for your Celery tasks.

Example logging configuration in `tasks.py`:

python
```python
import logging

logging.basicConfig(level=logging.INFO)

@celery.task(bind=True)
def add(self, x, y):
    logger.info(f"Adding {x} and {y}")
    result = x + y
    logger.info(f"Result: {result}")
    return result 
```
#### 2. Flower Monitoring

Flower is a real-time web application monitoring tool for Celery. It provides detailed information about task execution, including failures and retries.

To start Flower, run:

bash
```bash
(venv)$ celery -A app_factory.celery flower --port=5555 
```
Visit `http://localhost:5555` in your browser to view the Flower dashboard. You can see task details, retry counts, and error messages.

#### 3. Debugging with PDB

You can use the Python debugger (PDB) to debug Celery tasks. Insert `import pdb; pdb.set_trace()` in your task code to set a breakpoint.

Example in `tasks.py`:

python
```python
@celery.task(bind=True)
def add(self, x, y):
    import pdb; pdb.set_trace()  # Debugger breakpoint
    result = x + y
    return result 
```
Run your Celery worker in the terminal and execute the task. The worker will pause execution at the breakpoint, allowing you to inspect variables and step through the code.

### Conclusion

In this chapter, you learned how to implement robust error handling and debugging strategies for Celery tasks. By using `try-except` blocks, task retries, custom error messages, and notifications, you can ensure that your tasks handle failures gracefully. Additionally, logging, Flower, and PDB can help you debug issues effectively. In the next chapter, we will explore how to integrate Celery with SQLAlchemy and manage database transactions within tasks.

### Chapter 8: Integrating Celery with SQLAlchemy

In this chapter, we will explore how to integrate Celery with SQLAlchemy, a popular ORM for managing database transactions. Proper integration ensures that your Celery tasks can interact with the database effectively, maintaining data consistency and integrity.

### Objectives

-   Set up SQLAlchemy in your FastAPI project
-   Integrate SQLAlchemy with Celery tasks
-   Manage database transactions within Celery tasks

### Setting Up SQLAlchemy

#### 1. Install SQLAlchemy

Add SQLAlchemy and its dependencies to your `requirements.txt` file:

text
```text
SQLAlchemy==1.4.39
alembic==1.7.7
```
Install the new dependencies:

bash
```bash
(venv)$ pip install -r requirements.txt 
```
#### 2. Configure SQLAlchemy

Create a `database.py` file to configure SQLAlchemy:

python
```python
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

SQLALCHEMY_DATABASE_URL = "sqlite:///./test.db"

engine = create_engine(SQLALCHEMY_DATABASE_URL, connect_args={"check_same_thread": False})
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

Base = declarative_base() 
```
#### 3. Create Database Models

Define your database models in a `models.py` file:

python
```python
from sqlalchemy import Column, Integer, String
from .database import Base

class User(Base):
    __tablename__ = "users"

    id = Column(Integer, primary_key=True, index=True)
    name = Column(String, index=True)
    email = Column(String, unique=True, index=True) 
```
#### 4. Create Database Tables

Use Alembic to handle database migrations. Initialize Alembic in your project:

bash
```bash
(venv)$ alembic init alembic
```

Edit `alembic.ini` to set the SQLAlchemy URL:

ini

```ini
sqlalchemy.url = sqlite:///./test.db
```

Update `alembic/env.py` to include your models:

python
```python
from logging.config import fileConfig
from sqlalchemy import engine_from_config
from sqlalchemy import pool
from alembic import context

# this is the Alembic Config object, which provides
# access to the values within the .ini file in use.
config = context.config

# Interpret the config file for Python logging.
# This line sets up loggers basically.
fileConfig(config.config_file_name)

# add your model's MetaData object here
# for 'autogenerate' support
from app.models import Base  # Add this line
target_metadata = Base.metadata

# other values from the config, defined by the needs of env.py,
# can be acquired:
# my_important_option = config.get_main_option("my_important_option")
# ... etc.

def run_migrations_offline():
    """Run migrations in 'offline' mode.

    This configures the context with just a URL
    and not an Engine, though an Engine is also acceptable
    here.  By skipping the Engine creation we don't even need
    a DBAPI to be available.

    Calls to context.execute() here emit the given string to the
    script output.

    """
    url = config.get_main_option("sqlalchemy.url")
    context.configure(
        url=url, target_metadata=target_metadata, literal_binds=True
    )

    with context.begin_transaction():
        context.run_migrations()

def run_migrations_online():
    """Run migrations in 'online' mode.

    In this scenario we need to create an Engine
    and associate a connection with the context.

    """
    connectable = engine_from_config(
        config.get_section(config.config_ini_section), prefix="sqlalchemy.", poolclass=pool.NullPool
    )

    with connectable.connect() as connection:
        context.configure(connection=connection, target_metadata=target_metadata)

        with context.begin_transaction():
            context.run_migrations()

if context.is_offline_mode():
    run_migrations_offline()
else:
    run_migrations_online() 
```
Generate the initial migration:

bash
```bash
(venv)$ alembic revision --autogenerate -m "create users table"
``` 

Apply the migration:

bash
```bash
(venv)$ alembic upgrade head
```

### Integrating SQLAlchemy with Celery

#### 1. Using SQLAlchemy Sessions in Celery Tasks

Update `tasks.py` to use SQLAlchemy sessions:

python
```python
from .database import SessionLocal
from .models import User

celery = create_celery()

@celery.task(bind=True)
def add_user(self, name, email):
    db = SessionLocal()
    try:
        user = User(name=name, email=email)
        db.add(user)
        db.commit()
        db.refresh(user)
    except Exception as exc:
        db.rollback()
        logger.error(f"Error adding user {name}: {exc}")
        raise self.retry(exc=exc)
    finally:
        db.close()
    return user.id 
```
#### 2. Managing Transactions

Ensure that transactions are properly managed within your Celery tasks to maintain data integrity. Use `try-except` blocks to handle any errors and roll back transactions if needed.

### Testing Celery Tasks with SQLAlchemy

To test the integration, add routes to your FastAPI application that trigger the Celery tasks.

Update `routers.py`:

python
```python
from fastapi import APIRouter, HTTPException
from celery.result import AsyncResult
from .tasks import add_user

api_router = APIRouter()

@api_router.post("/tasks/add_user")
def run_add_user_task(name: str, email: str):
    task = add_user.apply_async((name, email))
    return {"task_id": task.id}

@api_router.get("/tasks/{task_id}")
def get_task_status(task_id: str):
    task = AsyncResult(task_id)
    if task.state == 'PENDING':
        return {"task_id": task.id, "state": task.state}
    elif task.state != 'FAILURE':
        return {"task_id": task.id, "state": task.state, "result": task.result}
    else:
        return {"task_id": task.id, "state": task.state, "error": str(task.info)} 
```
Start the FastAPI application and Celery worker, then test the task by sending a request to add a user.

### Conclusion

In this chapter, you learned how to integrate Celery with SQLAlchemy and manage database transactions within Celery tasks. This ensures that your tasks can interact with the database effectively, maintaining data consistency and integrity. In the next part, we will move on to deployment, testing, and best practices for using Celery with FastAPI.


## Part 3: Deployment, Testing, and Best Practices

### Chapter 9: Containerizing FastAPI, Celery, and Redis with Docker

In this chapter, we will explore how to containerize your FastAPI application along with Celery and Redis using Docker. Containerization simplifies the deployment process and ensures that your application runs consistently across different environments.

### Objectives

-   Set up Docker for FastAPI, Celery, and Redis
-   Create Dockerfiles for each service
-   Configure Docker Compose to orchestrate the services
-   Run and test the containerized application

### Setting Up Docker

#### 1. Install Docker

If you haven’t already, install Docker by following the instructions on the Docker website.

#### 2. Create Dockerfiles

Create Dockerfiles for the FastAPI application and Celery worker.

##### Dockerfile for FastAPI

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

# Make port 8000 available to the world outside this container
EXPOSE 8000

# Define environment variable
ENV NAME FastAPI

# Run app.py when the container launches
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"] 
```
##### Dockerfile for Celery

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

# Define environment variable
ENV NAME Celery

# Run Celery worker when the container launches
CMD ["celery", "-A", "app_factory.celery", "worker", "--loglevel=info"] 
```
#### 3. Create a Docker Compose Configuration

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
#### 4. Build and Run the Containers

Use Docker Compose to build and run the containers:

bash
```bash
$ docker-compose up --build 
```
Docker Compose will build the images and start the containers for FastAPI, Celery, and Redis. You should see logs indicating that the services are running.

#### 5. Test the Containerized Application

Open your browser and navigate to `http://localhost:8000`. You should see your FastAPI application running.

To test Celery tasks, use a tool like `curl` or Postman to send requests to the FastAPI endpoints that trigger Celery tasks.

Example:

bash
```bash
$ curl -X POST "http://localhost:8000/tasks/add_user" -H "Content-Type: application/json" -d '{"name": "John Doe", "email": "john.doe@example.com"}
``` 

### Best Practices

#### 1. Use Environment Variables

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
#### 2. Use Multi-stage Builds

Optimize your Dockerfiles using multi-stage builds to reduce the size of your Docker images. Example for FastAPI:

dockerfile
```docker
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

EXPOSE 8000

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"] 
```
#### 3. Use Health Checks

Configure health checks to ensure that your services are running properly. Update `docker-compose.yml`:

yaml
```yaml
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost:8000/health-check"]
  interval: 30s
  timeout: 10s
  retries: 3 
```
#### 4. Use Volumes for Persistent Storage

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
### Conclusion

In this chapter, you have learned how to containerize your FastAPI application along with Celery and Redis using Docker. You created Dockerfiles for each service, configured Docker Compose to orchestrate the services, and tested the containerized application. By following best practices, you can ensure that your containerized application is secure, efficient, and maintainable. In the next chapter, we will cover validating Celery tasks.

### Chapter 10: Validating Celery Tasks

In this chapter, we will explore techniques for validating Celery tasks to ensure that they execute correctly and reliably. Proper validation can help you catch errors early and ensure that your tasks are robust and reliable.

### Objectives

-   Validate input data for Celery tasks
-   Use Pydantic for data validation
-   Write unit tests for Celery tasks

### Validating Input Data

#### 1. Using Pydantic for Data Validation

Pydantic is a powerful data validation library that integrates seamlessly with FastAPI. You can use Pydantic models to validate the input data for your Celery tasks.

Update `tasks.py` to use Pydantic models for input validation:

python
```python
from pydantic import BaseModel, EmailStr, ValidationError

class UserInput(BaseModel):
    name: str
    email: EmailStr

@celery.task(bind=True, autoretry_for=(Exception,), retry_kwargs={'max_retries': 5}, retry_backoff=True)
def add_user(self, user_data):
    try:
        user = UserInput(**user_data)
    except ValidationError as e:
        logger.error(f"Validation error: {e}")
        self.update_state(state='FAILURE', meta={'exc': str(e), 'task': 'add_user'})
        return

    db = SessionLocal()
    try:
        new_user = User(name=user.name, email=user.email)
        db.add(new_user)
        db.commit()
        db.refresh(new_user)
    except Exception as exc:
        db.rollback()
        logger.error(f"Error adding user {user.name}: {exc}")
        raise self.retry(exc=exc)
    finally:
        db.close()
    return new_user.id 
```
#### 2. Updating API Endpoints

Update your FastAPI endpoints to accept and validate input data using Pydantic models:

Update `routers.py`:

python
```python
from pydantic import BaseModel

class UserRequest(BaseModel):
    name: str
    email: EmailStr

@api_router.post("/tasks/add_user")
def run_add_user_task(user_request: UserRequest):
    task = add_user.apply_async((user_request.dict(),))
    return {"task_id": task.id}
```
### Writing Unit Tests for Celery Tasks

#### 1. Setting Up Testing Environment

Create a `tests` directory in your project root and add a `test_tasks.py` file to write your unit tests.

Install `pytest` and `pytest-celery` for testing:

bash
```bash
(venv)$ pip install pytest pytest-celery
``` 

#### 2. Writing Tests for Celery Tasks

Write tests to validate the functionality of your Celery tasks. Use the Celery test harness to test tasks in isolation.

Example `test_tasks.py`:

python
```python
import pytest
from celery.result import EagerResult
from app.tasks import add_user
from app.models import User
from app.database import SessionLocal, Base, engine

# Create a new database for testing
Base.metadata.create_all(bind=engine)

@pytest.fixture(scope='module')
def db_session():
    session = SessionLocal()
    yield session
    session.close()

@pytest.mark.celery(result_backend='redis://localhost:6379/0')
def test_add_user(celery_app, celery_worker, db_session):
    celery_app.conf.task_always_eager = True
    user_data = {"name": "John Doe", "email": "john.doe@example.com"}
    result = add_user.apply(args=(user_data,))
    assert isinstance(result, EagerResult)
    assert result.result is not None

    user = db_session.query(User).filter_by(email="john.doe@example.com").first()
    assert user is not None
    assert user.name == "John Doe" 
```
Run the tests using `pytest`:

bash
```bash
(venv)$ pytest
```
### Conclusion

In this chapter, you learned how to validate Celery tasks using Pydantic for input data validation. You also wrote unit tests to ensure that your Celery tasks execute correctly. By validating input data and writing tests, you can catch errors early and ensure that your tasks are reliable and robust. In the next chapter, we will cover monitoring Celery tasks with Flower.

### Chapter 11: Monitoring Celery with Flower

In this chapter, we will explore how to use Flower, a real-time web application monitoring tool for Celery, to monitor and manage your Celery tasks. Flower provides a comprehensive interface to inspect task execution, monitor task states, and manage workers.

### Objectives

-   Set up Flower for monitoring Celery tasks
-   Use Flower to monitor task execution
-   Manage workers and queues using Flower

### Setting Up Flower

#### 1. Install Flower

Add Flower to your `requirements.txt` file:

text
```text
flower==2.0.1 
```
Install the new dependency:

bash
```bash
(venv)$ pip install -r requirements.txt
```

#### 2. Configure Flower in Docker Compose

Update your `docker-compose.yml` file to include a service for Flower:

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

  flower:
    image: "mher/flower"
    command: ["--broker=redis://redis:6379/0"]
    ports:
      - "5555:5555"
    depends_on:
      - redis 
```
#### 3. Start Flower

Use Docker Compose to start the Flower service along with the other services:

bash
```bash
$ docker-compose up --build
```

Flower will be accessible at `http://localhost:5555`.

### Using Flower to Monitor Task Execution

#### 1. Access Flower Dashboard

Open your browser and navigate to `http://localhost:5555`. You will see the Flower dashboard, which provides an overview of your Celery tasks and workers.

#### 2. Inspecting Tasks

Click on the "Tasks" tab in the Flower dashboard to see the list of tasks. You can inspect the details of each task, including its state, result, arguments, and execution time.

#### 3. Task States

Flower displays various states of tasks, including:

-   **PENDING**: The task is waiting to be executed.
-   **STARTED**: The task has been started by a worker.
-   **SUCCESS**: The task executed successfully.
-   **FAILURE**: The task failed to execute.
-   **RETRY**: The task is being retried after a failure.

#### 4. Task Details

Click on a task ID to view detailed information about the task, including its traceback in case of failure, and its result.

### Managing Workers and Queues Using Flower

#### 1. Workers

Click on the "Workers" tab to see the list of active workers. Flower displays the status of each worker, including the number of tasks it has processed and its uptime.

#### 2. Control Workers

Flower allows you to control your workers directly from the dashboard. You can:

-   **Start/Stop**: Start or stop a worker.
-   **Restart**: Restart a worker.
-   **Shutdown**: Shutdown a worker gracefully.

#### 3. Queues

Flower also provides insights into your task queues. Click on the "Queues" tab to see the list of queues and the number of tasks in each queue.

### Monitoring Alerts and Notifications

Flower can be configured to send alerts and notifications based on task events. You can integrate Flower with various notification services, such as email or Slack, to receive real-time alerts about task failures or other significant events.

### Conclusion

In this chapter, you learned how to set up Flower to monitor and manage your Celery tasks. Flower provides a comprehensive interface to inspect task execution, monitor task states, and manage workers. By using Flower, you can gain valuable insights into your task processing and ensure that your Celery tasks are running smoothly. In the next chapter, we will cover using Broadcaster and Python-Socket.IO for real-time notifications.

### Chapter 12: Using Broadcaster and Python-Socket.IO for Real-time Notifications

In this chapter, we will explore how to use Broadcaster and Python-Socket.IO to send real-time notifications from Celery tasks to the frontend. This allows you to provide immediate feedback to users about the status of long-running tasks.

### Objectives

-   Set up Broadcaster for real-time messaging
-   Integrate Python-Socket.IO with FastAPI
-   Send real-time notifications from Celery tasks to the frontend

### Setting Up Broadcaster

#### 1. Install Broadcaster

Add Broadcaster and related dependencies to your `requirements.txt` file:

text
```text
broadcaster==0.2.0
python-socketio==5.3.0
```
Install the new dependencies:

bash
```bash
(venv)$ pip install -r requirements.txt
```

#### 2. Configure Broadcaster

Create a `broadcaster.py` file to configure Broadcaster:

python
```python
from broadcaster import Broadcast

broadcast = Broadcast("redis://localhost:6379") 
```
### Integrating Python-Socket.IO with FastAPI

#### 1. Install FastAPI-Socket.IO

Install `fastapi-socketio` to integrate Socket.IO with FastAPI:

bash
```bash
(venv)$ pip install fastapi-socketio 
```
#### 2. Update FastAPI Application

Update your `main.py` to integrate Socket.IO:

python
```python
from fastapi import FastAPI
from fastapi_socketio import SocketManager
from .broadcaster import broadcast

app = FastAPI()
sio = SocketManager(app=app)

@app.on_event("startup")
async def startup():
    await broadcast.connect()

@app.on_event("shutdown")
async def shutdown():
    await broadcast.disconnect()

@sio.on('connect')
async def connect(sid, environ):
    print('Client connected:', sid)

@sio.on('disconnect')
async def disconnect(sid):
    print('Client disconnected:', sid)

@app.get("/")
async def root():
    return {"message": "Hello World"} 
```
### Sending Real-time Notifications from Celery Tasks

#### 1. Update Celery Tasks

Update `tasks.py` to send real-time notifications using Broadcaster and Socket.IO:

python
```python
from .broadcaster import broadcast
from .main import sio

@celery.task(bind=True)
def add_user(self, user_data):
    try:
        user = UserInput(**user_data)
    except ValidationError as e:
        logger.error(f"Validation error: {e}")
        self.update_state(state='FAILURE', meta={'exc': str(e), 'task': 'add_user'})
        return

    db = SessionLocal()
    try:
        new_user = User(name=user.name, email=user.email)
        db.add(new_user)
        db.commit()
        db.refresh(new_user)
        
        # Send a real-time notification
        sio.emit('task_update', {'status': 'User added', 'user_id': new_user.id})
    except Exception as exc:
        db.rollback()
        logger.error(f"Error adding user {user.name}: {exc}")
        raise self.retry(exc=exc)
    finally:
        db.close()
    return new_user.id
```    

### Frontend Integration

#### 1. Install Socket.IO Client

Install the Socket.IO client in your frontend application:

html
```html
<script src="https://cdn.socket.io/4.0.0/socket.io.min.js"></script>
``` 

#### 2. Connect to the Socket.IO Server

Add the following JavaScript code to connect to the Socket.IO server and listen for real-time notifications:

javascript
```java
<script>
  const socket = io("http://localhost:8000");

  socket.on("connect", () => {
    console.log("Connected to server");
  });

  socket.on("task_update", (data) => {
    console.log("Task Update:", data);
    // Update the UI based on the received data
  });

  socket.on("disconnect", () => {
    console.log("Disconnected from server");
  });
</script>
``` 

### Conclusion

In this chapter, you learned how to use Broadcaster and Python-Socket.IO to send real-time notifications from Celery tasks to the frontend. This allows you to provide immediate feedback to users about the status of long-running tasks, enhancing the user experience. By integrating real-time messaging into your application, you can keep users informed and engaged.

This concludes Part 3: Deployment, Testing, and Best Practices. You are now equipped with the knowledge to deploy, test, and monitor your FastAPI and Celery applications effectively.

## Part 4: Advanced Topics

### Chapter 13: Scaling Celery Workers

In this chapter, we will explore how to scale Celery workers to handle a higher load. Scaling workers is essential for ensuring that your application can process tasks efficiently, even as the number of tasks increases.

### Objectives

-   Understand the principles of scaling Celery workers
-   Configure Celery for horizontal scaling
-   Use Docker and Kubernetes for worker orchestration

### Principles of Scaling Celery Workers

#### 1. Horizontal vs. Vertical Scaling

-   **Horizontal Scaling**: Adding more worker instances to handle increased load.
-   **Vertical Scaling**: Increasing the resources (CPU, RAM) of existing worker instances.

Horizontal scaling is often preferred for distributed systems as it provides better fault tolerance and can be managed more efficiently.

#### 2. Load Balancing

Load balancing ensures that tasks are distributed evenly across multiple workers. This prevents any single worker from becoming a bottleneck.

### Configuring Celery for Horizontal Scaling

#### 1. Setting Up Redis as the Message Broker

Ensure that your Celery configuration uses Redis as the message broker, which supports horizontal scaling:

python
```python
from celery import Celery

app = Celery(
    'tasks',
    broker='redis://localhost:6379/0',
    backend='redis://localhost:6379/0'
) 
```
#### 2. Configuring Concurrency

Adjust the concurrency settings in your Celery configuration to control the number of worker processes:

python

```python
app.conf.update(
    worker_concurrency=4,  # Number of concurrent worker processes
)
``` 

#### 3. Starting Multiple Workers

You can start multiple Celery workers on different machines or containers to handle more tasks. Use the `-n` flag to assign unique names to each worker:

bash
```bash
celery -A tasks worker --loglevel=info -n worker1@%h
celery -A tasks worker --loglevel=info -n worker2@%h
``` 

### Using Docker for Worker Orchestration

#### 1. Docker Compose for Multiple Workers

Update your `docker-compose.yml` file to define multiple Celery worker services:

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

  worker1:
    build:
      context: .
      dockerfile: Dockerfile.celery
    depends_on:
      - redis
    environment:
      - CELERY_BROKER_URL=redis://redis:6379/0
      - CELERY_RESULT_BACKEND=redis://redis:6379/0
    command: celery -A app_factory.celery worker --loglevel=info -n worker1@%h

  worker2:
    build:
      context: .
      dockerfile: Dockerfile.celery
    depends_on:
      - redis
    environment:
      - CELERY_BROKER_URL=redis://redis:6379/0
      - CELERY_RESULT_BACKEND=redis://redis:6379/0
    command: celery -A app_factory.celery worker --loglevel=info -n worker2@%h

  redis:
    image: "redis:6-alpine"
    ports:
      - "6379:6379"

  flower:
    image: "mher/flower"
    command: ["--broker=redis://redis:6379/0"]
    ports:
      - "5555:5555"
    depends_on:
      - redis
```       

#### 2. Running Multiple Workers

Use Docker Compose to build and run the containers, which will start multiple Celery workers:

bash
```bash
$ docker-compose up --build
``` 

### Using Kubernetes for Worker Orchestration

Kubernetes can manage and scale your Celery workers across a cluster of nodes. Here’s how to set up a basic configuration.

#### 1. Define Kubernetes Deployment

Create a `deployment.yaml` file to define your Celery worker deployment:

yaml
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: celery-workers
spec:
  replicas: 3
  selector:
    matchLabels:
      app: celery
  template:
    metadata:
      labels:
        app: celery
    spec:
      containers:
      - name: celery-worker
        image: your-docker-image:latest
        command: ["celery", "-A", "app_factory.celery", "worker", "--loglevel=info"]
        env:
        - name: CELERY_BROKER_URL
          value: "redis://redis-service:6379/0"
        - name: CELERY_RESULT_BACKEND
          value: "redis://redis-service:6379/0" 
```

#### 2. Define Redis Service

Create a `redis-service.yaml` file for the Redis service:

yaml
```yaml
apiVersion: v1
kind: Service
metadata:
  name: redis-service
spec:
  ports:
  - port: 6379
  selector:
    app: redis
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - name: redis
        image: redis:6-alpine
        ports:
        - containerPort: 6379 
```
#### 3. Apply the Kubernetes Configuration

Apply the configuration files to your Kubernetes cluster:

bash
```bash
$ kubectl apply -f redis-service.yaml
$ kubectl apply -f deployment.yaml
``` 

Kubernetes will manage the deployment and ensure that the specified number of Celery worker pods are running. It will also handle scaling up or down based on the configuration.

### Conclusion

In this chapter, you learned how to scale Celery workers to handle a higher load using both Docker and Kubernetes. By configuring horizontal scaling and using orchestration tools, you can ensure that your application can process tasks efficiently, even as the load increases. In the next chapter, we will cover optimizing task performance.

### Chapter 14: Optimizing Task Performance

In this chapter, we will explore various strategies for optimizing the performance of Celery tasks. Optimizing task performance ensures that your application can handle a high volume of tasks efficiently, reducing latency and improving overall responsiveness.

### Objectives

-   Use task prioritization and routing
-   Optimize task serialization and deserialization
-   Implement task prefetching
-   Optimize worker configuration and resource usage

### Task Prioritization and Routing

#### 1. Task Prioritization

You can assign different priorities to tasks to ensure that high-priority tasks are executed before low-priority ones. Use the `priority` argument when sending tasks:

python
```python
@celery.task
def high_priority_task(data):
    # Process high priority task
    pass

@celery.task
def low_priority_task(data):
    # Process low priority task
    pass

# Send tasks with different priorities
high_priority_task.apply_async(args=[data], priority=0)  # Highest priority
low_priority_task.apply_async(args=[data], priority=9)  # Lowest priority 
```
#### 2. Task Routing

You can route tasks to specific queues based on their type or priority. Update your Celery configuration to define queues and routes:

python
```python
app.conf.task_routes = {
    'app.tasks.high_priority_task': {'queue': 'high_priority'},
    'app.tasks.low_priority_task': {'queue': 'low_priority'},
}

app.conf.task_queues = {
    'high_priority': {
        'exchange': 'high_priority',
        'exchange_type': 'direct',
        'binding_key': 'high_priority',
    },
    'low_priority': {
        'exchange': 'low_priority',
        'exchange_type': 'direct',
        'binding_key': 'low_priority',
    },
}
```

### Optimizing Task Serialization and Deserialization

#### 1. Choosing the Right Serialization Format

The default JSON serializer is often sufficient, but you can choose other serializers like `msgpack` or `pickle` for better performance.

Update your Celery configuration to use a different serializer:

python
```python
app.conf.task_serializer = 'msgpack'
app.conf.result_serializer = 'msgpack'
app.conf.accept_content = ['json', 'msgpack'] 
```
### Implementing Task Prefetching

#### 1. Prefetch Limit

Celery workers can prefetch tasks to improve throughput. Adjust the prefetch limit to control how many tasks a worker fetches at a time:

python
```python
app.conf.worker_prefetch_multiplier = 4  # Number of tasks to prefetch per worker process
```
### Optimizing Worker Configuration and Resource Usage

#### 1. Concurrency and Pool Size

Configure the concurrency and pool size of your Celery workers to optimize resource usage:

python
```python
app.conf.worker_concurrency = 4  # Number of concurrent worker processes
app.conf.worker_max_tasks_per_child = 100  # Number of tasks a worker can process before being replaced
``` 

#### 2. Monitoring and Autoscaling

Use monitoring tools like Flower or Prometheus to monitor worker performance and autoscale based on load.

Example using Flower:

bash
```bash
$ celery -A app_factory.celery flower --port=5555
``` 

Example using Prometheus and Grafana for monitoring:

yaml
```yaml
prometheus:
  image: prom/prometheus
  ports:
    - "9090:9090"
  volumes:
    - ./prometheus.yml:/etc/prometheus/prometheus.yml

grafana:
  image: grafana/grafana
  ports:
    - "3000:3000"
  volumes:
    - ./grafana.db:/var/lib/grafana/grafana.db 
```
### Conclusion

In this chapter, you learned various strategies for optimizing the performance of Celery tasks. By using task prioritization and routing, optimizing serialization and deserialization, implementing task prefetching, and configuring worker resource usage, you can ensure that your Celery tasks are processed efficiently and effectively. In the next chapter, we will cover securing Celery tasks.

### Chapter 15: Securing Celery Tasks

In this chapter, we will explore strategies for securing Celery tasks to protect your application from various security threats. Ensuring the security of your tasks is crucial for maintaining the integrity and confidentiality of your data.

### Objectives

-   Implement authentication and authorization for task execution
-   Use secure message brokers and backend storage
-   Handle sensitive data securely
-   Monitor and log task execution for security purposes

### Implementing Authentication and Authorization

#### 1. Task Authentication

Ensure that only authenticated users can trigger certain tasks. You can use FastAPI's dependency injection system to enforce authentication.

Example using OAuth2:

python
```python
from fastapi import Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer
from .tasks import add_user

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

def get_current_user(token: str = Depends(oauth2_scheme)):
    # Implement your token validation logic here
    user = validate_token(token)
    if not user:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid authentication credentials",
        )
    return user

@api_router.post("/tasks/add_user")
def run_add_user_task(user_request: UserRequest, current_user: User = Depends(get_current_user)):
    task = add_user.apply_async((user_request.dict(),))
    return {"task_id": task.id}
```     

#### 2. Task Authorization

Ensure that only authorized users can trigger certain tasks. Implement role-based access control (RBAC) or other authorization mechanisms as needed.

Example using role-based access control:

python
```python
def get_current_user(token: str = Depends(oauth2_scheme)):
    user = validate_token(token)
    if not user:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid authentication credentials",
        )
    if not user.has_role("admin"):
        raise HTTPException(
            status_code=status.HTTP_403_FORBIDDEN,
            detail="Not authorized to perform this action",
        )
    return user
``` 

### Using Secure Message Brokers and Backend Storage

#### 1. Secure Message Brokers

Ensure that your message broker (e.g., Redis, RabbitMQ) is configured securely. Use TLS/SSL to encrypt communication between Celery and the message broker.

Example with Redis:

yaml
```yaml
redis:
  image: "redis:6-alpine"
  ports:
    - "6379:6379"
  command: redis-server --requirepass "yourpassword" --tls-port 6379 --port 0 --tls-cert-file /path/to/cert.pem --tls-key-file /path/to/key.pem --tls-ca-cert-file /path/to/ca.pem
```

Update Celery configuration to use secure Redis:

python
```python
app.conf.broker_url = 'rediss://:yourpassword@localhost:6379/0'
app.conf.result_backend = 'rediss://:yourpassword@localhost:6379/0
``` 

#### 2. Secure Backend Storage

Ensure that your result backend storage is configured securely. Use TLS/SSL to encrypt communication between Celery and the result backend.

Example with Redis:

python
```python
app.conf.result_backend = 'rediss://:yourpassword@localhost:6379/0
``` 

### Handling Sensitive Data Securely

#### 1. Encrypting Sensitive Data

Encrypt sensitive data before storing it in the result backend. Use libraries like `cryptography` for encryption.

Example of encrypting sensitive data:

python
```python
from cryptography.fernet import Fernet

# Generate a key for encryption
key = Fernet.generate_key()
cipher = Fernet(key)

@celery.task(bind=True)
def add_user(self, user_data):
    user = UserInput(**user_data)
    encrypted_email = cipher.encrypt(user.email.encode())
    user.email = encrypted_email.decode()

    db = SessionLocal()
    try:
        new_user = User(name=user.name, email=user.email)
        db.add(new_user)
        db.commit()
        db.refresh(new_user)
    except Exception as exc:
        db.rollback()
        raise self.retry(exc=exc)
    finally:
        db.close()
    return new_user.id
``` 

#### 2. Securely Storing Encryption Keys

Store encryption keys securely, using environment variables or a secrets management service like AWS Secrets Manager or HashiCorp Vault.

### Monitoring and Logging Task Execution

#### 1. Logging Task Execution

Implement logging for task execution to detect and investigate security incidents.

Example of logging task execution:

python
```python
import logging

logger = logging.getLogger(__name__)

@celery.task(bind=True)
def add_user(self, user_data):
    logger.info(f"Adding user: {user_data}")
    try:
        user = UserInput(**user_data)
        db = SessionLocal()
        new_user = User(name=user.name, email=user.email)
        db.add(new_user)
        db.commit()
        db.refresh(new_user)
        logger.info(f"User added: {new_user.id}")
    except Exception as exc:
        db.rollback()
        logger.error(f"Error adding user {user_data}: {exc}")
        raise self.retry(exc=exc)
    finally:
        db.close()
    return new_user.id 
```
#### 2. Monitoring Task Execution

Use monitoring tools like Flower, Prometheus, and Grafana to monitor task execution and detect anomalies.

Example of integrating Flower for monitoring:

bash
```bash
$ celery -A app_factory.celery flower --port=5555
``` 

### Conclusion

In this chapter, you learned how to secure Celery tasks by implementing authentication and authorization, using secure message brokers and backend storage, handling sensitive data securely, and monitoring and logging task execution. By following these practices, you can protect your application from various security threats and ensure the integrity and confidentiality of your data. In the next chapter, we will explore case studies and real-world applications of Celery and FastAPI.

### Chapter 16: Case Studies and Real-world Applications

In this chapter, we will explore various case studies and real-world applications of Celery and FastAPI. These examples will demonstrate how to leverage the power of Celery and FastAPI to build robust and scalable applications.

### Objectives

-   Understand real-world use cases of Celery and FastAPI
-   Learn from case studies of successful applications
-   Apply best practices to your own projects

### Case Study 1: E-commerce Platform

#### Overview

An e-commerce platform needs to handle a variety of tasks, including sending order confirmation emails, processing payments, generating invoices, and updating inventory. Celery and FastAPI can be used to handle these tasks asynchronously, ensuring a smooth user experience.

#### Implementation

1.  **Sending Order Confirmation Emails**
    
    Celery task to send order confirmation emails:
    
    python
    ```python
    from celery import Celery
    from fastapi import FastAPI
    import smtplib
    from email.mime.text import MIMEText
    
    app = FastAPI()
    celery = Celery(__name__, broker='redis://localhost:6379/0')
    
    @celery.task
    def send_order_confirmation_email(order_id, customer_email):
        # Compose email
        msg = MIMEText(f"Your order {order_id} has been confirmed.")
        msg['Subject'] = 'Order Confirmation'
        msg['From'] = 'noreply@ecommerce.com'
        msg['To'] = customer_email
    
        # Send email
        with smtplib.SMTP('localhost') as server:
            server.sendmail(msg['From'], [msg['To']], msg.as_string())
    
    @app.post("/orders/{order_id}/confirm")
    async def confirm_order(order_id: int, customer_email: str):
        send_order_confirmation_email.apply_async((order_id, customer_email))
        return {"message": "Order confirmation email sent"}
	``` 
    
2.  **Processing Payments**
    
    Celery task to process payments:
    
    python
    ```python
    @celery.task
    def process_payment(order_id, payment_details):
        # Simulate payment processing
        time.sleep(5)
        return {"status": "success", "order_id": order_id}
    
    @app.post("/orders/{order_id}/pay")
    async def pay_order(order_id: int, payment_details: dict):
        result = process_payment.apply_async((order_id, payment_details))
        return {"task_id": result.id, "message": "Payment processing started"}
    ``` 
    
3.  **Generating Invoices**
    
    Celery task to generate invoices:
    
    python
    ```python
    @celery.task
    def generate_invoice(order_id):
        # Simulate invoice generation
        time.sleep(2)
        return {"invoice_id": f"INV-{order_id}", "order_id": order_id}
    
    @app.post("/orders/{order_id}/invoice")
    async def create_invoice(order_id: int):
        result = generate_invoice.apply_async((order_id,))
        return {"task_id": result.id, "message": "Invoice generation started"}
    ``` 
    
4.  **Updating Inventory**
    
    Celery task to update inventory:
    
    python
    ```python
    @celery.task
    def update_inventory(order_id, items):
        # Simulate inventory update
        for item in items:
            time.sleep(1)
        return {"status": "success", "order_id": order_id}
    
    @app.post("/orders/{order_id}/update_inventory")
    async def inventory_update(order_id: int, items: list):
        result = update_inventory.apply_async((order_id, items))
        return {"task_id": result.id, "message": "Inventory update started"}
    ``` 
    

### Case Study 2: Data Processing Pipeline

#### Overview

A data processing pipeline involves ingesting large datasets, processing the data, and generating reports. Celery and FastAPI can be used to handle the different stages of the pipeline asynchronously.

#### Implementation

1.  **Ingesting Data**
    
    Celery task to ingest data:
    
    python
    ```python
    @celery.task
    def ingest_data(data_source):
        # Simulate data ingestion
        time.sleep(10)
        return {"status": "success", "data_source": data_source}
    
    @app.post("/data/ingest")
    async def ingest(data_source: str):
        result = ingest_data.apply_async((data_source,))
        return {"task_id": result.id, "message": "Data ingestion started"}
    ``` 
    
2.  **Processing Data**
    
    Celery task to process data:
    
    python
    ```python
    @celery.task
    def process_data(dataset_id):
        # Simulate data processing
        time.sleep(15)
        return {"status": "success", "dataset_id": dataset_id}
    
    @app.post("/data/{dataset_id}/process")
    async def process(dataset_id: str):
        result = process_data.apply_async((dataset_id,))
        return {"task_id": result.id, "message": "Data processing started"}
    ``` 
    
3.  **Generating Reports**
    
    Celery task to generate reports:
    
    python
    ```python
    @celery.task
    def generate_report(dataset_id):
        # Simulate report generation
        time.sleep(5)
        return {"report_id": f"REP-{dataset_id}", "dataset_id": dataset_id}
    
    @app.post("/data/{dataset_id}/report")
    async def report(dataset_id: str):
        result = generate_report.apply_async((dataset_id,))
        return {"task_id": result.id, "message": "Report generation started"}
    ``` 
    

### Case Study 3: Machine Learning Model Training

#### Overview

Training machine learning models can be computationally intensive and time-consuming. Celery and FastAPI can be used to handle the training process asynchronously, allowing you to monitor progress and retrieve results once the training is complete.

#### Implementation

1.  **Starting Model Training**
    
    Celery task to start model training:
    
    python
    ```python
    @celery.task
    def train_model(model_id, training_data):
        # Simulate model training
        time.sleep(20)
        return {"status": "success", "model_id": model_id}
    
    @app.post("/models/{model_id}/train")
    async def train(model_id: str, training_data: dict):
        result = train_model.apply_async((model_id, training_data))
        return {"task_id": result.id, "message": "Model training started"}
    ``` 
    
2.  **Monitoring Training Progress**
    
    Use Flower or another monitoring tool to keep track of the training process:
    
    bash
    ```bash
    $ celery -A app_factory.celery flower --port=5555
    ``` 
    
3.  **Retrieving Training Results**
    
    Endpoint to retrieve training results:
    
    python
    ```python
    @app.get("/tasks/{task_id}/result")
    async def get_result(task_id: str):
        result = AsyncResult(task_id)
        if result.state == 'PENDING':
            return {"task_id": task_id, "state": result.state, "info": "Task is still in progress"}
        elif result.state != 'FAILURE':
            return {"task_id": task_id, "state": result.state, "result": result.result}
        else:
            return {"task_id": task_id, "state": result.state, "error": str(result.info)}
	``` 
    

### Conclusion

In this chapter, we explored various case studies and real-world applications of Celery and FastAPI. These examples demonstrate how to leverage the power of Celery and FastAPI to build robust and scalable applications for different use cases, including e-commerce platforms, data processing pipelines, and machine learning model training. By applying these best practices and examples to your own projects, you can create efficient and reliable systems that handle complex tasks asynchronously.

----------

This concludes "The Definitive Guide to Celery and FastAPI." You are now equipped with the knowledge to integrate, deploy, and optimize Celery with FastAPI for various applications. Thank you for following along, and best of luck with your future projects!

