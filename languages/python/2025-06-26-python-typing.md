## Python Typing Module
- [Type Hinting](#type-hinting)
  - [Basic Type Hints](#basic-type-hints)
    - [Uncommon Datatypes](#uncommon-datatypes)
  - [Complex Type Hints](#complex-type-hints)
  - [Define Types](#define-types)
- [Typing Module](#typing-module)
 

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

- ### Define Types
  * You can even define your own types using `classes` that can then be used for `type hints`

    ```python
    class NewUser:
        def __init__(self, name: str, age: int):
            self.name = name
            self.age = age

    def print_name_and_age(new_user: NewUser) -> None: # expected to return `None` value
        print(new_user.name, new_user.age)

    # initiate class with values
    n = NewUser("josh", 25)
    # call function
    print_name_and_age(n)
    ```

## Typing Module
* There is a python package that is built to support `type hints`, more commonly used for the uncommon python `datatypes`
  - The `typing` module

* In our examples above, we wanted more detailed `type hints` for `datatypes` like `list, sets, dict, tuples`, the `typing` module supports this

  ```python
  from typing import List, Dict
  
  numbers: List[int]: [1, 2, 3] # list of ints
  dict1 : Dict[str, str] # dict with key/value both being strings
  ```

* This module also has a variety of other use cases not native to python
  - `Any` Class indicates var can be any datatype

    ```python
    from typing import Any
 
    def example(data1: Any) -> None:
    ```

  - `Optional` Class indicates a var can be a specific type of `None`

    ```python
    from typing import Optional

    def dict_example(key_value: Optional[dict[str, str]]) -> None:
    ...
    ```

  - `Union` class indicates a var can be one of several types

    ```python
    from typing import Union

    def example(age: Union[str, int]) -> str:
    ...
    
    # Pipe operator is not another option that can be used as an alternative to this class
    def example(age: str | int) -> str:
    ...
    ```

  - `Type` class indicates that the variable will be a `class` not an instance of a `class`

    ```python
    from typing import Type
   
    def create_new_user(cls: Type[NewUser], name: str, age: int) -> NewUser:
        return cls(name, age)
    ```

  - `NewType` class allows you to create distinct types

    ```python
    from typing import NewType

    Id = NewType('Id', int)
    Age = NewType('Age', int)
 
    def user_age(student_id: Id, student_age: Age) -> None:
    ...

    # call function properly
    student_1 = user_age(Id(123432), Age(23))
    ```

- ### Type Aliases
  * The `typing` module also provides use with aliases, which assign type definitions to vars that can be used in `type hints`

    ```python
    Number = List[int]
    def sum_nums(numbers: Number) -> int:
        return sum(numbers)
    ```
