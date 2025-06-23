---
# Classes and Objects<h1>
---

## Table of Contents
- [Overview](#overview)
- [Self Parameter](#self-parameter)
- [Variables](#variables)
- [Init Function](#init-function)
- [Str Function](#str-function)
- [Object Methods](#object-methods)

## Overview
* Python is an object oriented programming language
  - Almost everything in python is an object with its own properites and methods
* A `Class` is like an object constructor for creating objects
  - Should be used if you have attributes and functions that naturally belong together that operate on that data(attributes)

```python
class Beginning:
    x = 5

print(Beginning.x) # this will return 5
```

## Self Parameter
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

* NOTE: `classes` cannot be empty, but if you must create an empty class, use the `pass` statement
  - A class SHOULD contain at least 2 methods

  ```python
  class Person:
      pass
  ```

## Variables
* There are 2 types of variables that can be defined in a class
  - `Class Variables`: which are vars shared across all instances of a class, defined at the class level
  - `Instance Variables`: which are vars unique to an instance of a class, defined within methods
  
    ```python
    class Mine:
        # class variable
        animal = "Dog"
       
        def __init__(self, name):
            # instance variable `name`, specific to an instance of this class
            # may differ depending on args passed when this class is initiated
            self.name = name
    
    ini1 = Mine("Bro")
    ini2 = Mine("Sis")
 
    # no matter the instance, the class var will return the same value
    print(ini1.animal) # returns Dog
    print(ini2.animal) # returns Dog

    # instance vars may differ based on initiation args passed
    print(ini1.name) # returns Bro
    print(ini2.name) # returns Sis

    # Update class variable
    Mine.animal = "Cat"
    print(ini1.name, ini2.name) # returns Cat Cat

    # Update instance variable
    ini1.name = "Neice"
    ini2.name = "Nephew"
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
* Objects (classes) can also contain methods (functions that belong to the object)

  ```python
  class Person:
      def __init__(self, name, age):
          self.name = name
          self.age = age

      def __str__(self):
          return f"{self.name}({self.age})"

      def myfunc(self): # method defined belonging to this object
          print(f"My name is {self.name} and I am {self.age}")

  p1 = Person("Jonny, 45)
  p1.myfunc() # this will return "My name is Jonny and I am 45"
  ```
