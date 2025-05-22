<h1>Tuples</h1>

* `Tuples` are immutable (cannot be changed) data that can be written with parenthese or as a set of values separated by commas
  - we saw this when we returned multiple values in a python function
  - multiple datatypes can be added to a tuple

  ```python
  return val1, val2
 
  # create a tuple
  var1 = 1, 2, 3 
  var1 = (1, 2, 3)  

  # tuple
  var1 = (1,)

  # Not a tuple
  var1 = (1)
  ```

<h2>Reading Data from a tuple</h2>

* You can read data from a `tuple` the same way you read data from a list

```python
var = (1, True)
var[0] # returns 1
var[1] # returns True

# you can iterate over tuples as well
for item in var:
    print(item)

# tuples in functions
def tuple_creation(num1: int, num2: int):
    results = num1 * num2
    if (results == 0):
        return None, False
    return results, True

product, pass_or_fail = tuple_creation(14, 15)
# we can pass the tuple values from the return into 2 separate vars we can utilize later
print(product) # returns 210
print(pass_or_fail) # return True

# arithmetic operators with tuples
tuple = ("python",) * 3 # creates tuple '("python", "python", "python")'

# len() on tuples
print(len(tuple))

# convert a list to a tuple
list = [1, 2, 3, 4, 5]
tuppy = tuple(list)
```

* The built in methods `index()` and `count()` as recorded [here](https://eoyebami.github.io/languages/python/2024-08-10-python-lists.html) can be used in tuples as well
