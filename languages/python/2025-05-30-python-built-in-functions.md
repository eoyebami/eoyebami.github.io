<h1>Built in Functions</h1>

* Noting on a couple python built in functions to be aware of

<h2>All()</h2>

* Returns `True` if all elements of the iterable ae `True` or if iterable is empty
  - Replaces multiple `and` statements in python

```python
if all([True, True, True]): # the equivalent of && in bash
    print("All conditions are True")

l = [0, 1, 1]
print(all(l)) # Will return False

print(all(ele > 0 for ele in l)) # this will return false

# iterate over list of functions
def is_int(num):
    if not isinstance(num, int):
        return False
    return True

def is_str(string):
    if not isinstance(string, str):
        return False
    return True
check_types = [(is_int, 1), (is_str, "hello world!")]

if all(check_type(value) for check_type, value in check_types):
    print("Satisfied Condition")
```

<h2>Any()</h2>

* Similarly to the `all()` except returns `True` if any of the iterables is `True`, if iterable is empty it returns `False`
  - Replaces multiple `or` statements in python

```python
if any([True, True, True]): # the equivalent of || in bash
    print("All conditions are True")

l = [0, 1, 1]
print(any(l)) # Will return True

print(any(ele > 0 for ele in l)) # this will return True 

# iterate over list of functions
def is_int(num):
    if not isinstance(num, int):
        return False
    return True

def is_str(string):
    if not isinstance(string, str):
        return False
    return True
check_types = [(is_int, 1), (is_str, "hello world!")]

if any(check_type(value) for check_type, value in check_types):
    print("Satisfied Condition")
```

<h2>dict()</h2>

* Creates dictionaries

```python
dictionary = dict () # creates empty dict
my_dict = dict(name="john", class="A")
print(my_dict) # outputs {'name': 'john', 'class': 'A'}

# tuples can be passed
pairs = [("a", 1), ("b", 2), ("c", 3)]
my_dict = dict(pairs)
```

<h2>enumerate()</h2>

* Adds a counter to an item in an iterable and turns it to something that can be looped
  - Returns list of tuple with associated counter

```python
a = ["Geeks", "for", "Geeks"]

# Iterating list using enumerate to get both index and element
for i, name in enumerate(a):
    print(f"Index {i}: {name}") # will produce "Index 0: Geeks"
```

<h2>eval()</h2>

* Evaluates the specified expression, if it is a legal python statement, it will execute it

```python
eval('print(55)') # returns 55

# protect against malicious code 
```

<h2>callable()</h2>

* Returns `True`, if the object appears to be callable and `False` if not

```python
def is_int(num):
    if not isinstance(num, int):
        return False
    return True

print(callable(is_int)) # returns True
```

<h2>ascii()</h2>

* Returns a readable version of any object

```python
x = ascii("My name is Ståle")
# replaces non-ascii char with escaped char
```

<h2>breakpoint()</h2>

* built-in tool for debugging code, pausing execution at that point to inspect program state
  - Debugging has a set of commands:
    * `c`: continue execution
    * `q`: quit the debugger/execution
    * `n`: step to next line within the same function
    * `s`: step to next ine in this function or a called function

```python
def calculate_sum(a, b):
    breakpoint()  # Execution will pause here
    result = a + b
    return result

print(calculate_sum(5, 3))
```

<h2>divmod()</h2>

* Takes 2 numeric args and returns a tuple with the quotient and the remainder of their division

```python
print(divmod(10, 3) # returns (3, 1)
```
