## Part 2: Foundation and Concepts
### Chapter 8: Integrating Celery with SQLAlchemy

In this chapter, we will explore how to integrate Celery with SQLAlchemy, a popular ORM for managing database transactions. Proper integration ensures that your Celery tasks can interact with the database effectively, maintaining data consistency and integrity.

#### Objectives

-   Set up SQLAlchemy in your FastAPI project
-   Integrate SQLAlchemy with Celery tasks
-   Manage database transactions within Celery tasks

#### Setting Up SQLAlchemy

##### 1. Install SQLAlchemy

Add SQLAlchemy and its dependencies to your `requirements.txt` file:

text
```text
SQLAlchemy==1.4.39
alembic==1.13.2
```
Install the new dependencies:

bash
```bash
(venv)$ pip install -r requirements.txt 
```
##### 2. Configure SQLAlchemy

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
##### 3. Create Database Models

Define your database models in a `models.py` file:

python
```python
from sqlalchemy import Column, Integer, String
from database import Base

class User(Base):
    __tablename__ = "users"

    id = Column(Integer, primary_key=True, index=True)
    name = Column(String, index=True)
    email = Column(String, unique=True, index=True) 
```
##### 4. Create Database Tables

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
from models import Base  # Add this line
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

#### Integrating SQLAlchemy with Celery

##### 1. Using SQLAlchemy Sessions in Celery Tasks

Update `task.py` to use SQLAlchemy sessions:

python
```python
from database import SessionLocal
from models import User

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
##### 2. Managing Transactions

Ensure that transactions are properly managed within your Celery tasks to maintain data integrity. Use `try-except` blocks to handle any errors and roll back transactions if needed.

#### Testing Celery Tasks with SQLAlchemy

To test the integration, add routes to your FastAPI application that trigger the Celery tasks.

Update `routers.py`:

python
```python
from fastapi import APIRouter, HTTPException
from celery.result import AsyncResult
from .tasks import add_user

api_router = APIRouter()

class User(BaseModel):
    name: str
    email: str

@api_router.post("/tasks/add_user")
def run_add_user_task(name: str, email: str):
    task = add_user.apply_async((name, email))
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
Start the FastAPI application and Celery worker, then test the task by sending a request to add a user.
bash
```bash
(venv)$ curl -X POST "http://localhost:8000/tasks/add_user" -H "Content-Type: application/json" -d '{"name":"taymoor","email":"abcd@gmail.com"}'
```
```bash
(venv)$ curl -X GET "http://localhost:8000/tasks/{task_id}"
```

#### Conclusion

In this chapter, you learned how to integrate Celery with SQLAlchemy and manage database transactions within Celery tasks. This ensures that your tasks can interact with the database effectively, maintaining data consistency and integrity. In the next part, we will move on to deployment, testing, and best practices for using Celery with FastAPI.
