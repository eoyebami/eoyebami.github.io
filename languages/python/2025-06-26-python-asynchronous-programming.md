## Asynchronous Programming
- [Overview](#overview)
- [Coroutines](#coroutines)
- [Await](#await)
- [Tasks](#tasks)

## Overview
* To understand `asynchronous programming` we must first understand what `synchronous programming`
  - `Synchronous Programming` is essentially what python does by default, running a bunch of statements in sequential order
  - `Asychronous Programming` does not need to run in sequential order
    * For example, while function `foo()` is running, waiting for something (networking packets, user input, etc), our processor will move ahead with other statements that it can execute while its free to do so

## Coroutine
* In order to use `asynchronous programming` we must create a coroutine
  - Functions defined with `async def` are coroutine functions
  - Coroutines can suspend execution at await expressions allowing other statements within the python code to run concurrently

  ```python
  import asyncio

  # async forms a wrapper around a regular function
  # when u call this function it will return a coroutine object
  # just like a function, this coroutine object can be executed
  async def main():
      print("Asynchronous Programming")
  ```

* To execute this coroutine object, you must `await` its execution
  - Attempting to run the above codeblock will return a `RuntimeWarning` that the coroutine object has not been `awaited`
  - Whenever we create an `async` programming we must define an `async event-loop` 
    * You can do this with `asyncio.run()`, which id designed to be the main entry point of running async programs
    * `awaiting` a coroutine is also running the coroutine

  ```python
  async def main():
      print("Asynchronous Programming")

  asyncio.run(main()) # creates the event-loop, and passing the coroutine to that loop
  # this also runs the coroutine, effectively awaiting it
  ```

## Await
* As stated earlier, in order to run a coroutine it must be awaited
  - `await` tells python to wait until the execution of that coroutine to complete

  ```python
  import asynio
  
  async def main():
      print("Asynchronously Programming")
      await foo("text") # calling a coroutine function, must also be awaited to run

  async def foo(text):
      print(text)
      await asyncio.sleep(1) # a coroutine function, must be awaited to run 

  asyncio.run(main()) # awaits the coroutine
  ```

* The above code, though using async programs, will technically still run synchronously, since the `await` will force the process to wait for the completion of the coroutine before continuing with other statements

## Tasks
* To actually configure the program to run `asynchronously` you must use what is known as a `tasks`
