<h1>Python Operators and Expressions</h1>

<h2>Python Operators</h2>

* Python is filled with different `operators` and the values to which they are applied to are called `operands`

* `Addition`: `print(1 + 1)`
  - Not only works with `int` and `floats` but `str` for concatenation
* `Subtraction`: `print(1 - 1)`
* `Multiplication`: `print(1 * 1)`
  - Not only works with `int` and `floats` but `str` for concating string against `int` provided
* `Division`: `print(6 / 2)`
  - divisions produce a `float`
  - Python converts operation into a `float` before executing, this is called `implicit type casting`
  - To avoid this you can run divisions using `print(6 // 2)` which will produce an `int`
    * It does this by just removing whatever decimal is being produced
    * This will also round down to the less integer number
* `Exponents`: `print(2 ** 2)`
* `Modulo`: `print(10 % 5)`
  - outputs the remainder produced from the division of the two numbers

- NOTE: python mathematics follows `PEMDAS`
 
  ```python
  print((3 + 3) ** 2 * 3 / 4 + 2)
  # parentheses and exponents will be done first before the multiplication, division and the addition
  ```

* Suppose we want to round the value produced in our mathematical equation, we can use the `round()`
  
  ```python
  # round() will round the number based on whats in the tenths place in the value
  print(round(1.75)) 
  # this will output 2.0
  # much better output than simply flooring the value

  # rounc() takes 2 inputs, the number you are rounding and the number of digits in the decimal place to round to
  print(round(1.7592, 2))
  # this will ouput 1.76
  ```

<h2>Assignment Operators</h2>

* Python Assignment Operators, allow us to adjust values
  - `+=`: increments value
  - `-=`: substracts value
  - `*=`: multiples value
  - `/=`: divides value
  - `**=`: exponential value
  - `//=`: division rounding
  - `%=`: modulo value

  ```python
  score = 1
  score += 1
  print(score)
  # this will output 2
  ```

<h2>Expressions</h2>

* `Expressions` are a combination of values, vars, and operators


<h2>Tip Calculator Example</h2>

```python
print("Welcome to the tip calculator!")
total_bill = float(input("What was the total bill: \n"))
tip_percentage = int(input("How much tip would you like to give (10, 12, or 15): \n"))
split = int(input("How many people to split the bill: \n"))
calculation = round((total_bill * (tip_percentage / 100 + 1)/split), 2)
# the parentheses in this case is pretty unnecessary, but I added it to make it more readable
print(f"Each person should pay ${calculation}")

```

