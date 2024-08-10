<h1>Try and Except Statements</h1>

* In situations within our `program`, where we want to handle unexpected errors, we can a `conditional execution` structure that is `python` native, called `try/except`
  - This is for `sequential executions` that you know may cause problems
  - Therefore, you can set another set of instructions to run should an error occur in the `try` block

* Example code:

  ```python
  try:
    # do this
  except:
    # do this should try block fail
  ```

* `Python` will first try executing the sequence of statements in the `try` block, if there are no errors it will skip the `except` block and proceed
* This is known as `catching an exception`

* Typical when a `program` fails, python will trigger a series of `traceback` functions, that acts as a parent `except` block for the `program`, which provide information as to why the script failed
