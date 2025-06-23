## Try and Except Statements
- [Overview](#overview)
- [Exception Variation](#exception-variation)
- [Exception Hierarchy](#exception-hierarchy)
- [Multi Exception Branch](#multi-exception-branch)

### Overview
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

### Exception Variation
* Python allows you to make exceptions for specific errors in your `try/except` statements

  ```python
  try 
      # do this
  except ValueError:
      # do this only if there is a ValueError
  except NameError:
      # do this only is there is a NameError
  except:
      # catch all for remaining errors
  ```

* Catching the exception and using it as a var is also possible

  ```python
  try:
      # do this
  exception NameError as e:
      print(f"Exception: {e}")
  except Exception as e:
      print(f"Exception: {e}")
  ```

### Exception Hierarchy
* `Python3` has 63 built in exceptions, which can be found [here](https://docs.python.org/3/library/exceptions.html)
  - These exceptions form a tree hierarchy
    * For example a `ZeroDivisionError` is a more specific case of an `ArthmeticError`
    * An `ArthmeticError` is a more specific case of an `Exception`
    * An `Exception` is a more specific case of a `BaseException`
* Because of this hierarchy, you need to be careful on how you order your except branch in your python code

  ```python
  try:
      # do this
  except ZeroDivisionError: # placed first as it is more specific than the ArithmeticError
      # do this
  except ArithmeticError: # if this was placed first, this would take precedence over the ZeroDivisionError
      # do this
  except:
      # do this
  ```

### Multi Exception Branch
* You can also define multiple exceptions in one branch
 
  ```python
  try:
      # do this
  except (ZeroDivisionError, ValueError, NameError) as e: # using a tuple to define multiple exceptions in this branch
      # do this  

  # you can manually raise an exception for testing
  raise ZeroDivisionError 
  ```

