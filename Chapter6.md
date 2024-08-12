## Part 2: Foundation and Concepts
### Chapter 6: Task Management with Celery

In this chapter, we will explore various aspects of task management with Celery. We will cover how to create, schedule, and manage tasks effectively. Additionally, we will discuss task retries and error handling.

#### Objectives

-   Create and manage Celery tasks
-   Schedule tasks
-   Handle task retries and errors
-   Ensure tasks work with SQLAlchemy

#### Creating and Managing Celery Tasks

##### 1. Creating Tasks

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
##### 2. Scheduling Tasks

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
##### 3. Running the Celery Beat Scheduler

To run the Celery beat scheduler alongside your worker, use the following command:

bash
```bash
(venv)$ celery -A task.celery worker --loglevel=info --beat
```

#### Handling Task Retries and Errors

##### 1. Retrying Tasks

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
##### 2. Error Handling

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
#### Ensuring Tasks Work with SQLAlchemy

When working with SQLAlchemy, it's important to ensure that tasks use the correct database session. You can use the `scoped_session` from SQLAlchemy to manage sessions effectively.

##### 1. Setting Up SQLAlchemy

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
##### 2. Using SQLAlchemy in Tasks

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
#### Conclusion

In this chapter, you have learned how to create, manage, and schedule Celery tasks. You also explored how to handle task retries and errors effectively. Additionally, you integrated SQLAlchemy with Celery tasks to ensure proper database session management. In the next chapter, we will dive deeper into error handling and debugging Celery tasks.