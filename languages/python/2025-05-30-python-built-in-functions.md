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

# can be used to replace range(len())
for i, in enumerate(a):
    value = a[i]
    print(f"Index {i}: {value}")
```

<h2>set()</h2>

* Used to create a sec object (which are unordered collections of unique elements)
  - Automatically delete duplicates
  - Unordered, cannot be referred by index or key
  - `True` and `1` are considered the same value in a set, same with `False` and `0`
  - [Methods for sets](https://www.w3schools.com/Python/python_ref_set.asp)

```python
empty_set = set() # creates an empty set
my_list = [1, 2, 2, 3]
my_set = set(my_list) # set value will be [1, 2, 3]

# can be initialized with {}
my_set = {1, 2, 3}
```

<h2>slice()</h2>

* Returns the sliced object containing elements in the given range
  - same args as the range()

```python
string = "hello world"
sliced_obj = slice(5, 11)
print(sliced_obj) # returns "world"

listing = [1, 2, 3]
sliced_list = slice(2)
print(sliced_list) # returns [1, 2]
```

<h2>reversed()</h2>

* reverses the sequence of a list, and returns as iterator object without modifying original sequence

```python
a = [1, 2, 3, 4, 5]
rev_a = reversed(a) # returns [5, 4, 3, 2, 1]
```

<h2>filter()</h2>

* filters a given sequence with help of a function that tests each element in the sequence to be true or not

```python
def is_even(n):
    return n % 2 == 0

nums = [1, 2, 3, 4, 5, 6]
result = filter(is_even, nums) # runs iterable through a function that returns True/False, and filters for all True iterations

print(list(result))
```

<h2>format()</h2>

* method formats specified value(s) and inserts them with string placeholders

```python
txt1 = "My name is {fname}, I'm {age}".format(fname = "John", age = 36)
txt2 = "My name is {0}, I'm {1}".format("John",36) # indexes can be used to set when called
txt3 = "My name is {}, I'm {}".format("John",36)
# you can format how strings get places or format numbers and even perform conversions
```

<h2>frozenset()</h2>

* returns an unchangeable frozenset object

```python
mylist = ['apple', 'banana', 'cherry']
x = frozenset(mylist)
```

<h2>globals()</h2>

* Returns a dict representing all the global variables in the current module or script

```python
globals() # as with all dicts, methods apply here
```

<h2>locals()</h2>

* Returns a dict representing the local variables and methods in the current module or script

```python
locals() # as with all dicts, methods apply here
```

<h2>map()</h2>

* Applies an iterable against a function to return a map object which is also an iterable

```python
def square(x):
    return x * x

numbers = [1, 2, 3, 4]
squared_numbers = map(square, numbers) 
print(list(squared_numbers))  # Output: [1, 4, 9, 16]
# NOTE: iterables do no reset or rewind, so once u run the map if you try to print it again it will return an empty list
# to avoid to this convert to list before assigning to a var
squared_numbers = list(map(square, numbers))
```

<h2>open()</h2>

* Opens a file and returns it as a file object
  * There are different modes to open a file in:
    1. `r`: read (default)
    2. `w`: write, truncates file first
    3. `x`: open for exclusive creation, fails if file already exists
    4. `a`: open for writing, appending to the end of file if it exists
    5. `b`: binary mode (e.g. images)
    6. `t`: text mode
    7. `+`: open for updating (reading and writing)
  * default encoding is ASCII, you can change it to something like `utf-8`

```python
f = open(file='images.txt', mode='r')
print(f.read())

# recommend to use this with the "with" statement so it automatically closes the file once done

with open("images.txt", "r") as file:
    contents = file.read()
    print(contents)

with open("images.txt", "a", encoding="utf-8") as file:
    file.write("First Image")

# iterate with for loop
for i, image in enumerate(images):
    with open("images.txt", "a", encoding="utf-8") as file"
        file.write(f"{i}: {image}")
```

<h2>range()</h2>

* Generates a sequence of numbers 
  - `start`: where to begin the index (default: 0)
  - `stop`: where to stop the index, no included
  - `step`: how to increment

```python
range(0, 10, 1) # returns from 0 to 9
range(10) # returns from 0 to 9
range(1, 10, 2) # returns 1, 3, 5, 7, 9

# commonly used to iterate over lists
list1 = [1, 2]
for i in range(len(list1)):
    print(i)
```


<h2>next()</h2>

* Iterates through iterators one by one

```python
mylist = iter(["apple", "banana", "cherry"])
x = next(mylist)
print(x)
x = next(mylist, "finished")
```

<h2>isinstance()</h2>

* Returns `True` if the specified object is the specified type

```python
isinstance("hello", str)
isinstance("hello", (str, int, float, bool, list, dict, tuple)) # u can also provide a tuple of types to verify against
```

<h2>issubclass()</h2>

* returns `True` if the specified object is a subclass of a specified object

```python
class Animal:
    pass

class Dog(Animal):
    pass

print(issubclass(Dog, Animal))  # True
print(issubclass(Animal, Dog))  # False
print(issubclass(Dog, (Animal, object)))  # True, works like the tuple in `isinstance`
```

<h2>iter()</h2>

* Creates an iterator object

```python
x = iter(["apple", "banana", "cherry"])
print(next(x)) # use next object to call iterator
print(next(x))
print(next(x))
# will print subsequent item in list each time iterator is called 

# u can specify when iteration should stop, if the first arg is a callable
def get_input():
    return input("Enter something (type 'exit' to stop): ")

for user_input in iter(get_input, 'exit'):
    print(f"You entered: {user_input}")
```

<h2>len()</h2>

* Determines the len of items within an object

```python
mylist = ["apple", "banana", "cherry"]
print(len(mylist)) # returns 3

s = "GeeksforGeeks"
print(len(s)) # returns 13
```

<h2>eval()</h2>

* Evaluates the specified expression, if it is a legal python statement, it will execute it
  - You can specify a dicts for managing global and local methods and variables
    * `globals`: dict to specify available global methods and vars
    * `locals`: dict to specify available local methods and vars
    * e.g [example](https://www.geeksforgeeks.org/eval-in-python/)

```python
eval('print(55)') # returns 55
```

<h2>exec()</h2>

* Works exactly like `eval()` except it accepts code block and returns None rather than the expressions output

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
