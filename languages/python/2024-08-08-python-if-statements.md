<h1>Python If Statements</h1>

* Python allows us to define if/else statements that define what should happen based on a condition
  - it follows the below logic:
 
    ```python
    if condition:
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
  * `Logical Operators`:
    - `and`
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
