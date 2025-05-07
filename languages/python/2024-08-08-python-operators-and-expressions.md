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

<h2>Bitwise Operators</h2>

* `Bitwise` operatoes allow you to manipulate single bits of data
  - `&`: conjunction(AND)
    
    ```python
    # bin() converts an int to a binary in string format
    # bits are calculated in base-2, so divide by 2 and aggregate remainders in reverse order
    print(bin(15)) # 0b1111 or 00001111
    print(bin(22)) # 0b10110 or 00010110
    
    # returns 1 if both bits are 1, otherwise it will return 0
    print(15 & 22) # returns 6, which in binary is 0b110 or 00000110
    ```

  - `|`: disjunction(OR)

    ```python
    # similar concept to a conjunction
    # returns a 1 of either both or one of the bits are 1
    print(15 & 22) # 0b1111 & 0b10110 returns 0b11111 or 00011111 which is equal to 31
    # to calculate reversion back to decimal exponent 2 by the position of each bit then multiply by number and add it all together
    # ignore all 0s, since their product will always be 0
    ```

  - `~`: negation(NOT)

    ```python
    # this only takes one argument and returns the inverse of each bit
    print(~22) # 0b10110 returns -0b10111 11101001 which is -23
    # will return negative value of number - 1
    ```

  - `^`: exclusive(XOR)

    ```python
    # this operator returns 1 if only one of the bits are 1
    print(15 ^ 22) # # 0b1111 & 0b10110 returns 0b11001 or 00011001 which is equal to 25
    ```

* Assignment operators also apply
  - `&=`
  - `|=`
  - `^=`

* `Bitshifting` can also be done
  * NOTE: take precendence over bitwise operators
  - `>>`: shift bits to the right
  - `<<`: shift bits to the left

  ```python
  print(22 >> 1) # shift bits to the right by 1 from 0b10110 to 0b1011
  # a binary right shift is the same as an integer division (//, round down) orders of magnitude of 2 (2 ** x)
  # print(22 // 2) or print(22 // 4) if shifted twice, or print(22 // 8) if shifted three times
  print(22 << 1) # shift bits to the left by 1 from 0b10110 to 0b101100
  # a binary left shift is the same as an multiplication (*) orders of magnitude of 2 (2 ** x)
  # print(22 * 2) or print(22 * 4) if shifted twice, or print(22 * 8) if shifted three times
  ```

* NOTE: cannot be used with floating point numbers

