<h1>Python Primitive Data Types</h1>

* Python has a variety of different datatypes, the most common being:
  - `Strings(str)`: string of characters
    * Held in `""`, each character within a string can be indexed: `print("Hello"[0])` will output `"H"`
    * You can use negative indicies to count backwards as well 
  - `Integer(int)`: whole numbers
    * `print(1)`
    * Large numbers that are typically broken up with `,` for it to be more human-readable, can be broken up with ` _ ` in python instead: `print(1_000_000)`
  - `Float`: Floating Point numbers, numbers with decimals
    * `print(1.0)`
  - `Boolean`: True or False state 
    * `print(True)`: use title format for bools

<h2>Type Checking</h2>

* There are some functions in python that can only process specific datatypes
  - For instance the `len()`
     
    ```python
    len(1) 
    # This will return a TypeError, since the len() does not process int
    ```

* The `type()` will capture the type of any value passed to it

  ```python
  print(type(123))
  # will return int datatype
  ```

<h2>Type Casting/Conversion</h2>

* In python, there are ways to manipulate the type of a value, this is known as `type casting`
  - `int()`

  ```python
  print(int("123")) # converts value into integer type, that will be treated as such
  print(int("123") + int("456"))  # will return the addition of the 2 integers
  # trying to convert a value that is not obviously possible will result in a `ValueError`
  # converting a float to an int, will floar the number, removing all decimals
  ```

  - `float()`

  ```python
  print(float(1)) # converts value into a float type, that will be treated as such
  ```

  - `str()`
  
  ```python
  print(str(123)) # converts value to a string type, that will be treated as such
  ```

  - `bool()`
   
  ```python
   print(bool("True")) # converts the value to a boolean type, that will be treated as such
  ```

<h2>Fstrings</h2>

* Fstrings allow us to print multiple different datatypes as strings without typecasting, essentially just calling vars the same way you would do it in bash
 
  ```python
  cost = 19.99
  quantity = 3
  print(f"I want to purchase {quantity} cheesecakes for {cost} each")
  ```
