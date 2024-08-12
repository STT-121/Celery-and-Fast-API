## Part 3: Deployment, Testing, and Best Practices
### Chapter 10: Validating Celery Tasks

In this chapter, we will explore techniques for validating Celery tasks to ensure that they execute correctly and reliably. Proper validation can help you catch errors early and ensure that your tasks are robust and reliable.

#### Objectives

-   Validate input data for Celery tasks
-   Use Pydantic for data validation
-   Write unit tests for Celery tasks

#### Validating Input Data

##### 1. Using Pydantic for Data Validation

Pydantic is a powerful data validation library that integrates seamlessly with FastAPI. You can use Pydantic models to validate the input data for your Celery tasks.

Update `task.py` to use Pydantic models for input validation:

python
```python
from pydantic import BaseModel, EmailStr, ValidationError

celery = create_celery()

logger = logging.getLogger(__name__)

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
##### 2. Updating API Endpoints

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
#### Writing Unit Tests for Celery Tasks

##### 1. Setting Up Testing Environment

Create a `tests` directory in your project root and add a `test_tasks.py` file to write your unit tests.

Install `pytest` and `pytest-celery` for testing:

bash
```bash
(venv)$ pip install pytest pytest-celery
``` 

##### 2. Writing Tests for Celery Tasks

Write tests to validate the functionality of your Celery tasks. Use the Celery test harness to test tasks in isolation.

Example `test_tasks.py`:

python
```python
import pytest
from celery.result import EagerResult
from .tasks import add_user
from .models import User
from .database import SessionLocal, Base, engine

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
#### Conclusion

In this chapter, you learned how to validate Celery tasks using Pydantic for input data validation. You also wrote unit tests to ensure that your Celery tasks execute correctly. By validating input data and writing tests, you can catch errors early and ensure that your tasks are reliable and robust. In the next chapter, we will cover monitoring Celery tasks with Flower.
