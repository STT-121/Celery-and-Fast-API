## Part 2: Foundation and Concepts

### Chapter 5: Configuring Celery with FastAPI

In this chapter, we will configure Celery to work with our FastAPI application. We will set up the necessary configuration files and ensure that Celery can handle tasks properly.

#### Objectives

-   Configure Celery settings
-   Integrate Celery with FastAPI
-   Create and execute Celery tasks

#### Configuring Celery

##### 1. Update Configuration Settings

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

##### 2. Create a Celery Instance

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

##### 3. Define Celery Tasks

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

##### 4. Register Task Routes

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

#### Running the Application

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
#### Testing Celery Tasks

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

#### Conclusion

You have now successfully configured Celery with FastAPI and created some basic tasks. You can run and monitor these tasks to ensure they execute correctly. In the next chapter, we will dive deeper into managing Celery tasks, handling errors, and ensuring tasks work correctly with SQLAlchemy.
