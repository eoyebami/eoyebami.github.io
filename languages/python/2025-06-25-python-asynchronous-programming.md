## Asynchronous Programming
- [Overview](#overview)
- [Coroutines](#coroutines)
- [Await](#await)
- [Tasks](#tasks)
  - [Running Tasks Concurrently](#running-tasks-concurrently)
- [Futures](#futures)
- [Locks](#locks)

## Overview
* To understand `asynchronous programming` we must first understand what `synchronous programming`
  - `Synchronous Programming` is essentially what python does by default, running a bunch of statements in sequential order
  - `Asychronous Programming` does not need to run in sequential order
    * For example, while function `foo()` is running, waiting for something (networking packets, user input, etc), our processor will move ahead with other statements that it can execute while its free to do so
* More on `asyncio` can be found [here](https://docs.python.org/3/library/asyncio.html)

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

* You can also use a runner context manager for managing multiple async function calls in the same event loop

  ```python
  import asyncio
  
  with asyncio.Runner() as runner:
      runner.run(main())
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

* NOTE: when we await `asyncio.sleep()`, we tell python to pause this coroutine and let the event loop run other things while i wait

* The above code, though using async programs, will technically still run synchronously, since the `await` will force the process to wait for the completion of the coroutine before continuing with other statements

## Tasks
* To actually configure the program to run `asynchronously` you must use what is known as a `tasks`
* In the example below, python will run all the statements sequentially

  ```python
  import asynio

  async def main():
      print("Asynchronously Programming")
      await foo("text") # calling a coroutine function, must also be awaited to run
      print("Completed")

  async def foo(text):
      print(text)
      await asyncio.sleep(1) # a coroutine function, must be awaited to run

  asyncio.run(main()) # awaits the coroutine
  ```

* Now if we want to free up the processor to handle other tasks while that coroutine is waiting, we must create a task
  - When we create a task, we essentially tell python to schedule that task to run in the background
  - During that scheduling, the processor is free to execute other tasks, which is why in the code below, `"Completed"` is printed before `"text"`

  ```python
  import asynio

  async def main():
      print("Asynchronously Programming")
      task = asyncio.create_task(foo("text")) # tells asyncio to execute coroutine as soon as it can, and to allow other code to run while this coroutine is staled
      # because creating the task does not block the processor, the print("Completed") is able to be executed
      # if we want to reestablish the "synchronous", we can also `await task` itself, to tell python to wait for this task to finish
      print("Completed")

  async def foo(text):
      print(text)
      await asyncio.sleep(1) # a coroutine function, must be awaited to run

  asyncio.run(main()) # awaits the coroutine

  # awaiting task
  import asynio

  async def main():
      print("Asynchronously Programming")
      task = asyncio.create_task(foo("text"))
      await task # when we await, we're pausing execution until its completed (unless its awaiting a asyncio.sleep())
      print("Completed")

  async def foo(text):
      print(text)
      await asyncio.sleep(1)

  asyncio.run(main()) # awaits the coroutine
  ```

* There can be multiple handoffs of resources using `asyncio`

  ```python
  import asynio

  async def main():
      print("Asynchronously Programming")
      task = asyncio.create_task(foo("text")) # this task will be scheduled to run soon
      await asyncio.sleep(0.5) # since this sleep is set to await, the task is allowed to continue to run once again
      print("Completed") # after 0.5 this print will also be allowed to run, since the foo() sleep is now active

  async def foo(text):
      print(text)
      await asyncio.sleep(1) # a coroutine function, must be awaited to run, during this await the processor is free to run the `print("Completed")`

  asyncio.run(main()) # awaits the coroutine
  ```
 
 - ### Running Tasks Concurrently
   * If we want to run a number of tasks concurrently, we can use `asyncio.gather()` rather than creating the tasks individually

     ```python
     import asyncio
           
     async def foo(text):
         print(text)
         await asyncio.sleep(1)
    
     async def main():
         tasks = [ foo(i) for i in range(10) ] # dynamically generating tasks, but we can call them one my one as separate args in `aynscio.gather()`
         await asyncio.gather(*tasks, return_exception=True) # the `*` unpackes the lists so each task becomes a separate argument in `asyncio.gather()`
         # return_exception=True: exceptions are treated as success and are aggregated in the results list
         # return_exception=False: exceptions are immediately propagated to the task that awaits
         return "completed"
     ```

    * Another way of running tasks concurrently, would be to use `asyncio.TaskGroup()`

      ```python
      import asyncio

      async def foo(text):
         print(text)
         await asyncio.sleep(1)

      async def main():
         # With TaskGroup(), if a task in a group fails with an exception, other than `asyncio.CancelledError` the remaining tasks in the group are also cancelled
         # You can terminate a taskgroup as well since its not natively supported
         async with asyncio.TaskGroup as tg: # using async with will wait for all tasks to complete, runs without blocking event loop
             for i in range(10):
                 tg.create_task(foo(i))
      ```

## Futures
* In the block of code below, we are running 2 concurrent task but want to retrieve the output of only one of them
  - We're trying to assign the output of an async function to a value, before that coroutine has completed execution
  - The nature by which `asyncio.create_task` works is by running in the background freeing up the execution to any other tasks
  - But because the executioner has been freed, its attempts to pass the value of a pending program to a var, which returns an error
  - We must `await` the task itself, so python waits for it to complete before assign the output of that coroutine to that variable
  - This is known as a `Future`
* Note, if a task is not awaited, should the function it resides it finishes execution, the program will exit before that task can complete it's run in the background
  - In the block of code below, we see that same issue
    - Main function only awaits task1 to completed before exiting the program

  ```python
  import asyncio
  
  async def fetch_data():
      print("starting fetch")
      await asyncio.sleep(2) # simulates api call, wait period for server to generate response, frees up processor to run the print_numbers()
      print("finished fetch") # won't run until `asyncio.sleep(2)` completes
      return {'data': 1}

  async def print_numbers():
      for i in range(10):
          print(i) # will print i
          await asyncio.sleep(0.25) # frees processor, but can't give execution back to any other tasks because asyncio.sleep(2) is still running for fetch_data()
          # this loop will continue, until another task can pick up execution while in its own `asyncio.sleep(0.25)

  async def main():
      task1 = asyncio.create_task(fetch_data()) # schedules coroutine to run in the background
      task2 = asyncio.create_task(print_numbers()) # schedules coroutine to run in the background
 
      value = await task1 # if we do not await, main function will attempt to assign the pending task to value, which will cause an error
      # should we await task2, the entire execution will run before program exits
      
      print(value)

  asyncio.run(main()) # awaits coroutine 
  ```

## Locks

* While working with `asynchronous programming` you may find the need to modify vars or objects which could cause issues like race conditions
  - Using `asyncio.Lock()` you can make sure an object is updated one at a time, make it safe for shared resources

  ```python
  import asyncio

  lock = asyncio.lock

  async with lock:
    results["sucess"].append(var)
  ```
