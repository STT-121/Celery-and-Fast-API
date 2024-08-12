# The Definitive Guide to Celery and FastAPI
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