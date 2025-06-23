---
# Classes and Objects<h1>
---

## Table of Contents
- [Init Function](#init-function)
* Python is an object oriented programming language
  - Almost everything in python is an object with its own properites and methods
* A `Class` is like an object constructor for creating objects
  - Should be used if you have attributes and functions that naturally belong together that operate on that data(attributes)


```python
class Beginning:
    x = 5

print(Beginning.x) # this will return 5
```

## Init Function

* All classes have a function called `__init__()`, which always runs when the class is initiated
  - It should be defined in your class to assign values to object properties or basically anything that is necessary for the class to function for whatever purpose it was created for
