---
# Classes and Objects<h1>
---

## Table of Contents
- [Self](#self)
- [Init Function](#init-function)
- [Str Function](#str-function)
- [Object Methods](#object-methods)

* Python is an object oriented programming language
  - Almost everything in python is an object with its own properites and methods
* A `Class` is like an object constructor for creating objects
  - Should be used if you have attributes and functions that naturally belong together that operate on that data(attributes)


```python
class Beginning:
    x = 5

print(Beginning.x) # this will return 5
```

## Self

* The `self` parameter is a reference to the currenct instance of a class and is used to access vars that belong to said class
  - It doesn't even have to be called `self` but it must be the first parameter of any function in that class

  ```python
  class Beginning:
      def __init__(self, name, age):
          silly.name = name
          silly.age = age

      def myfunc(not_silly): # not_silly is the first parameter in this function, therefore it is now the self param
          print(f"My name is {not_silly.name} and I am {not_silly.age}")
  ```

## Init Function

* All classes have a function called `__init__()`, which always runs when the class is initiated
  - It should be defined in your class to assign values to object properties or basically anything that is necessary for the class to function for whatever purpose it was created for

  ```python 
  class Beginning:
      def __init__(self, name, age):
          self.name = name
          self.age = age
  
  print(Beginning.name)
  ```

## Str Function

* The `__str__()` defines what should be returned when the class is represented as a string

  ```python
  class Person:
      def __init__(self, name, age):
          self.name = name
          self.age = age

      def __str__(self):
          return f"{self.name}({self.age})"

  p1 = Person("John", 36)
  print(p1) # returns "John(36)"
  ```

## Object Methods
