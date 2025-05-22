<h1>Python Functions</h1>

* A `function` is named sequence of statements that performs a computation
* When defining a `function`, you  specify the name and the sequence of statements
* You can later call that `function` by its name
* An example of a `function` is the `print()`

  ```python
  print("Hello World") # function call
  # here we've called the function and the "()" allow us to pass input into the function
  ```

* `Functions` typically take in and `argument` and returns a `result (return value)`.
  - In the `print()` example above, the argument we passed in was `"Hello World"`

<h2>Built-In Function</h2>

* `Python` comes set with predefined functions that do not need us to provide the function definition
  - `print()`: returns input
  - `max()`: returns largest value in a list, str input will return largest character in alphabetical order
  - `min()`: returns smallest value in a list, str input will return smallest character in alphabetical order
  - `len()`: returns int of number of items in a list, str inputs will return character count

<h2>Imported Functions</h2>

* Not all functions are built-in, there are some that you'll need to import
  - Like the `Random Madule`

  ```python
  import random # creates a module object named math
  ```

* This module object contains the functions and vars defined in the module
  - To call these functions, you must first specify the name of the module and the new of the function associated with that module
    * Formatted using `dot notation`
 
    ```python
    import random

    random.seed(1)
    print(random.random())

    ``` 

* More notes on the `random module` can be found [here](https://eoyebami.github.io/languages/python/2024-08-09-python-randomization.html)

<h2>Creating Functions</h2>

* A `function definition` specifies the name of a new function and the sequence of statements that execute when called
* `Syntax`:
  
  ```python
  # this can also be considered a compound statement
  # def is the reserved word that indicates that this is a function definition
  def greetings(): # the function is called 'greetings' and the empty '()' indicates that this function takes no inputs
      # this can also be considered the body of this compound statement
      name = input("What is your name?")
      print(name)

  ```

* Defining a `function` creates a var with the same name
* `Functions` can call different defined functions and when `python` is executing such a function:
  - It jumps to the body of the called `function` befoer jumping back to where it left off
  - `Python` is able to keep track of this, but when reading a `program` you need to follow the same flow in order to understand whats going on
  - Its also possible to define a function that calls another function before defining the function that it calls
    * This is because `python` doesn't run the sequence of statements within that function until it is actually called

* A `function definition` can also take input
  - The input we pass to `functions` are known as `arguments`
  - These `arguments` are assigned to variables called `parameters`
* `Syntax`:
  
  ```python
  # function definition
  def greetings(name): # name is the parameter
      print(name)

  # defining the value
  id = input("What is your name")

  # calling the function
  # passing in our input 'id' as our argument
  # this argument will be assigned the var 'name' as our parameter in our function
  greetings(id)

  ```

* `Expressions` can allso be applied to arguments when the function is called:
  
  ```python
  greetings(id * 4)
  # this argument will be evaluated before the function is called
  ```

<h2>Fruitful and Void Functions</h2>

* Some `functions` that we run yield results that we either need to see or reference later; these are known as `fruitful functions`
* Some `functions` that we run yield no results, but instead are meant to perform an action; these are known as `void functions`

* When we run `fruitful functions` we almost always want to do something with the results.
* When we run a function that does not output a `return value`, if we try to assign the functions stdout to a var, you will instead get a `None` value
  
  ```python
  def print_hello:
      print("Hello")

  >>> result = print_hello() 
  None
  >>> print(type(None))
  <class 'NoneType'>
  ```

* To return a result from a `function`, effectively, creating a `fruitful function`, we must use a `return` statement

  ```python
  def print_hello:
      output = print("Hello")
      return output
 
  >>> result = print_hello()
  Hello
  ```

* Here's an example using the `random module`

```python
import random
import string
 
def random_password_generator(add_special_char, length):
    if add_special_char:
        characters = string.ascii_letters + string.digits + string.punctuation
    else:
        characters = string.ascii_letters + string.digits

    random_password = "".join(random.choices(characters, k=length))
    return random_password # returns random_password

new_password = random_password_generator(True, 24)
print(new_password)

```

* Here's another example using a overtime pay calculator

```python
def computepay():
    standard_hours = 40
    hours = float(input("Enter Hours: "))
    rate = float(input("Enter Rate: "))

    if hours > standard_hours:
        overtime_rate = (rate * 1.5)
        overtime_hours = (hours - standard_hours)
        overtime_pay = (overtime_hours * overtime_rate)
    
    regular_pay = (rate * hours)
    total_pay = round((regular_pay + overtime_pay), 2)
    return total_pay
    
print(f"Pay: {computepay()}")

```

<h2>Arguments</h2>

* Arguments can be passed to functions

```python
def input_number(num):
    return int(input("Enter a number: ")) * num

input_number(10)
# passing in the 10, will set the num var in the function to what you passed that arg
# in the case that a function asks for multiple args, you can either pass them in the order specified or call it explicitly

def multiple(num1, num2):
    return num1 * num2

multiple(num1 = 4, num2 = 15):

# default values for args can also be set
def multiple(num1 = 4, num2 = 15):
    return num1 * nume2

# that way you can call the function without passing any args
multiple()
```

* You can also set default value types for arguments in a function

```python
def multiple(num1: int, num2: int):
    return num1 * num2

# you can even set default values while setting default value types
def multiple(num1: int = 4, num2: int = 15):
    return num1 * num2
```

* NOTE: This won't raise an error, to do so do this

```python
def multiple(num1: int = 4, num2: int = 15):
    if not isinstance(num1, int)
        raise TypeError("num1 must be an int")
    return num1 * num2

def multiple(num1: int = 4, num2: int = 15):
    if not all(isinstance(i, int) for i in(num1, num2)):
        raise TypeError("num1 and num2 must be an int")
    return num1 * num2

```

* NOTE: when you pass a var to a function and that function modifies the value, it will only be changed within that function as seen below:

```python
number = 1000
def multiple(num):
    num *= 200
    return num

print(multiple(number))
print(number)
# python creates another var called num and duplicates its value to that new var num in this function
# changes made only modify that new var called num
```

* NOTE: for lists, this is different, since the point to the memory address where this value is stored rather than the value itself
  - Making changes in the function will also change the original value, unless you slice the list

<h2>Return</h2>

* `Return` statements in functions stop the function
  - Returns also allow you to save the stdout of a function into a new var outside of the function

```python
def print_product(num1, num2):
    product = num1 * num2
    if (product == 0):
        return # if product equals 0, the functions returns and outputs nothing
    return product # if product does not equal 0, the function returns that value
``` 

* If no `return` is specified in the function, then the function will always return `None`
  - If `return` is called with no args, this too will return `None`

* You can return more than one value as well (returning a tuple)

```python
def print_product(num1, num2):
    product = num1 * num2
    if (product == 0):
        return None, False# if product equals 0, the functions returns and outputs nothing
    return product, True # if product does not equal 0, the function returns that value
    # you can also return like `return (product, True)`

product, result = print_product()
print(product) # Either None of the value
print(result) # Boolean
```

<h2>Scopes</h2>

* In functions, vars defined within a function can only be called within that functions scope

```python
def print_num():
    num = 5000
    print(num)
# calling num outside of this function will throw an error
```

* In order to make a scoped var global, you need to call the `global` statement

```python
def print_sum():
    global num = 5000
    print(num)

print(num)
```

