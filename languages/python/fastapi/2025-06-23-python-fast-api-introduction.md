## Introduction to Fast Api
- [Prerequisites](#prerequisites)
- [Understanding Fast Api](#understanding-fast-api)
- [Create Post Requests](#create-post-requests)

## Prerequisites

* Install `fastapi` packages as well as all optional dependencies
  
  ```python
  python3 -m venv venve
  source venv/bin/activate
  pip install fastapi[all] # all in brackets install optional deps
  ```

* You'll notice that `uvicorn` is also installed, which is the webserver that handles web connections for `FastApi`
* From here, create your first api
 
  ```python
  # app.py
  from fastapi import FastAPI

  app = FastAPI()

  @app.get("/")
  def root():
      return {"message": "Hello World"}
  ```

* Use `uvicorn` to run our api: `uvicorn app:app` -- `(<python-file-name>:<FastAPI-object>)`

  ```python
  INFO:     Started server process [1041322]
  INFO:     Waiting for application startup.
  INFO:     Application startup complete.
  INFO:     Uvicorn running on http://127.0.0.1:8000 (Press CTRL+C to quit)
  ```

## Understanding Fast API
* What we've done with the `FastAPI` object is to initiate it and define a `path operation` or `route` for our api
  - We have a couple components within a `path operation`
    1. `decorator`: turns function into a path operation that can be called
      - We call the `fastapi` instance and associate the http method that should be used to call that operation
      - In this case we use the `GET` method
      - We also define the `path` where this method should be called
    2. `path operation function`: defines logic to run and data to return when path is called

  ```python
  # path operation
  @app.get("/") # this is a decorator, defining a GET method at the root path
  def get_hello_world(): # path operation function
      return {"message": "Hello World"} # within this function, we can define logic that will be run when this operation is called
      # whatever we return in a path operation is the response that will be returned to the user
  ```

* Anytime you make a change to your api, you need to restart `uvicorn`
  - Workaround for this issue is to run `--reload` arg with uvicorn
    * `uvicorn` will monitor changes to your code and auto-reload when it detects changes
* NOTE: when a call is made against your api, the first path that is matched is what will run first

## Create Post Requests
* With `fastapi` we can generate a number of different requests and manipulate that in a number of different ways
* We'll be going over doing this with the `post` requests specifically about extracting data from the `Body`

```python
from fastapi import FastAPI

@app.get("/") # decorator
def hello_world(): # path operation
    return {"Greetings": "Hello World!"}

@app.post("/create")
def create():
    return {"message": "successfully created"}
```

* The above code is the standard way of creating a `post request` path operation
  - But the point of a `post request` is to upload data to the server and have the server respond
  - Within our `path operation` we may want to do something with whatever data was sent to the server

  ```python
  from fastapi import FastAPI
  from fastapi.params import Body

  @app.post("/create")
  def create(payload: dict = Body(...)): # since data typically sent is in json, we'll make it so the body is converted to a python dictionary
      return {"message": "successfully created post with title: {payload['title']} and content: {payload['content']}"}
  ```

* Now, if we want to test the functionality of our code, we can just run a curl:
  - `curl -X POST -H "Content-Type: application/json" http://127.0.0.1:8000/create -d '{"title": "Creation", "content": "here"}'`
