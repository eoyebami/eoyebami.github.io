<h1>Python If Statements</h1>

* Python allows us to define if/else statements that define what should happen based on a condition
  - it follows the below logic:
 
    ```python
    # a statement which consists of a header and a body and is usually identified by a colon at the end of the header
    # this is a compound statement
    if condition:
        # a sequence of statements within a compound statement are known as the body
        # do this
    else:
        # do this
    ```

  - example: Determine if a number is odd or even
  
    ```python
    print("Welcome to the Odd/Even Verifier!")
    num = float(input("What number would you like verified: \n"))
    remainder = (num % 2)

    if remainder == 0:
        print("This number is even")
    else:
        print("This number is not even")
    # be careful with the indentation or this may result in an IndentationError
    # could have used modulo operator within the conditional itself
    # if num % 2 == 0:
    # SHORT HAND
    if remainder == 0: print("This number is even")
    # SHORT HAND if/else
    print("This number is even") if remainder == 0 else print("This number is odd")
    ```

<h2>Conditional Operators</h2>

* You can define conditions in if/else statements by using a variety of `conditional operators`
  - Operators are just symbols in python, that servce a specific function
  * `Comparison Operators`: 
    - `>`: greater than
    - `<`: less than
    - `>=`: greater than or equal to
    - `<=`: less than or equal to
    - `==`: equal to
    - `!=`: not equal to
    - `is`: similar to `==`
  * `Logical Operators`:
    - `and`: if one of the conditions is false, `python` will stop evaluation of the rest of the logical exp (short-circuiting evaluation). However this may case issues, should the first condition succeed and the second condition result in a logical error
    - `or`
    - `not`

<h2>Nested If/Else Statements</h2>

* Nested if/else statements can also be used in python code
  - it follows the below logic:
  
    ```python
    if condition:
        # do this
        if condition:
            # do this
        else:
            # do this
    else:
        # do this
    ```

* This allows us to define conditions within conditions
* NOTE: the logic on these are difficult to parse, so its suggested to avoid these when possible

<h2>Elif Within If/Else Statements</h2>

* In cases where we have 3 separate conditions, we can use an `elif` in our if statement
  - it follows the below logic:
 
    ```python
    if condition:
        # do this
    elif condition2: 
        # do this
    else:
        # do this
    ```

<h2>Multi-Condition If statement</h2>

* We may run into a case in python, where more than one condition should be satisfied before proceeding
  - There are 2 ways to handle this, by using `and, or, not` operators
 
    ```python
    # in cases like this, I like to wrap the conditions in parentheses
    if (condition and condition):
        # uses guardian pattern to avoid logical errors with possible short-circuit evaluation
        # guard patterns take advantage of short-cir evals, by validating succeeding condition
        # do this
    elif (condition or condition):
        # do this
    elif not condition:
        # do this
    else:
        # do this
    ```

<h2>Example problem</h2>

```python
print("Welcome to Python Pizza Deliveries!")
size = input("What size pizza do you want? S, M or L: ")
pepperoni = input("Do you want pepperoni on your pizza? Y or N: ")
extra_cheese = input("Do you want extra cheese? Y or N: ")
bill = 0

if size == "S":
    bill += 15
elif size == "M":
    bill += 20
elif size == "L":
    bill += 25
else:
    print("This input is not accepted")

if pepperoni == "Y":
    if size == "S"
        bill += 2
    else:
        bill += 3

if extra_cheese == "Y":
    bill += 1

print(f"Your final bill is: ${bill}.")

```

NOTE: Simplify your python logic

  ```python
  # in this case where you have a condition where the same var is being called
  if age <= 25 and age >= 15:
  # this can actually be simplified to:
  if 25 >= age <= 15: 
  # this may be harder to understand 
  ```

<h2>While Conditions</h2>

* `While` conditions will allow you to execute code while a condition is true

```python
secret_number = 3
while guess != secret_number:
  guess = int(input("Guess a number from 1 to 5: "))
```

* `Else` blocks can also be used in a `while` conditional 

```python
secret_number = 3
while guess != secret_number:
  guess = int(input("Guess a number from 1 to 5: "))
else:
  print("Congrats, you guessed correctly!")
```

<h2>For loops</h2>

* `For` loops allow you to repeat code based on a certain amout of values 

```python
for i in range(10):
  print("i is: ", i)

list = [1, 2, 3]
for x in list:
  print("x is: ", x)
```

* You may want to break of out of the loop if a condition is satisfied

```python
list = [1, 2, 3]
for x in list:
  if(x == 2):
    break
  print("x is: ", x)
```

* You may want to skip an iteration in the loop rather than breaking the loop

```python
list = [1, 2, 3]
for x in list:
  if(x == 2):
    continue
  print("x is: ", x)
```
