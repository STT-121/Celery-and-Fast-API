# The Definitive Guide to Celery and FastAPI
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