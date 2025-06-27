## Python Typing Module
-[Type Hinting](#type-hinting)
  - [Basic Type Hints](#basic-type-hints)
    - [Uncommon Datatypes](#uncommon-datatypes)
  - [Complex Type Hints](#complex-type-hints)

## Type Hinting
* In python, `type hinting` involves the addition of annoations to vars, funcs, params, and return values to indicate their expected `datatypes`
* `Python` by default is a `dynamically-typed language`, meaning instead of declaring a type of var before using them, you simply declare a var and directly assign a value to it without specifying a type
  
  ```python
  x = 10 # x is an int
  x = "Hello World" # x is a string
  ```

* This offers increased flexibility, `type hints` come in because they improve code readability and error detection froom tools like `mypy` which utilizes these hints to check the consistency of types in your code
  - IDE's also utilize `type hints`
* NOTE: `type hints` are not enforeced by python, use `pydantic models` to enforce data validation

- ### Basic Type Hints
  * `Types Hints for Variables`
    
    ```python
    var1: type1 = value1
    age: int = 20 # hints this is an int
    user_name: str = "josh" # hints this is a string
    new_user: bool = True # hints this is a boolean
    # can be done for lists, tuples, sets, etc
    ```

  * `Type Hints in Functions`

    ```python
    def function_name(arg1: type1, arg2: type2, ...) -> return_type:
        # function body

    def announement(name: str, age: int) -> str: # hints datatype of params and hints that this function will return a string
        return f"User's name is {str} and they are {age} years old"

    # default values can also be set in these functions
    def announement(name: str = "josh", age: int = 20) -> str:
        return f"User's name is {str} and they are {age} years old"
    ```

  - #### Uncommon Datatypes
    * Type Hinting can also be done with less common `datatypes` like `list, floats, tuples, sets, dicts`
    
      ```python
      def multiple_decimals(num1: float = 2.3, num2: float = 4.6) -> float:
          return num1 * num2
 
      def sum_elements(numbers: list) -> int:
          return sum(numbers)
      ```

- ### Complex Type Hints
  * After python version 3.9 types `list, dict, tuple, and set` classes can be parameterized to provide more detailed `type hints`

    ```python
    numbers: list[int] = [1, 2, 3] # list of ints
    weights: dict[str, int] = {"josh": 1, "mack": 2} # dict with string keys and int values
    data: tuple[int, float, bool] = (1, 1.0, True)
    ```
