## Python Fast Api: Post Requests
- [Overview](#overview)
- [Create Post Requests](#create-post-request)

## Overview
* With `fastapi` we can generate a number of different requests and manipulate that in a number of different ways
* We'll be going over doing this with the `post` requests

## Create Post Requests

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
