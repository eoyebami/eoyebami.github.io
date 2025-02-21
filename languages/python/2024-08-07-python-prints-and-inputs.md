<h1>Hello World</h1>

<h2>Print Function</h2>

* Beginning my python journey by exploring the most basic of python functions, the `print()`

  ```python
  print("Hello World!")
  # here I've provide the function with a string that it should output on PyCharm, resulting in the following

  "/Users/eoyebami/PycharmProjects/100 Days of Code - The Complete Python Pro Bootcamp/.idea/VirtualEnvironment/bin/python" /Users/eoyebami/PycharmProjects/100 Days of Code - The Complete Python Pro Bootcamp/Day 1/task/task.py 
  Hello World!

  Process finished with exit code 0
  # First line is the location where the file was run
  # Second line is the ouput of the script
  # Thrid line is the exit code of said script
  
  print('''
  Hello
  World
  ''')
  # ''' allows you to print multi-lined strings

  # in cases where the string you're typing may be too long, you can break it up
  print("Hello"
        "World")
  # Hitting 'Enter' in your IDE will automatically do this for you
  ```

* The `print()` will always add a newline at the end of each output, this can be manipulated

  ```python
  print("Hello", end="")
  print("World!")
  # end argument, allows you replace the newline character with whatever you specify
  # in this case, we replace it with an empty value
  ```

* The `print()` also allows you concat and manipulate field separators in concatenations

  ```python
  print("Hello", "World!")
  # concats these two string and use " " as the default field separator
  # mathematically expressions, vars, and functions can also be called into the print
  print(12, type(12))
  print("1 + 1 =", 1+1)
  print("Hello", "World", sep="! ")
  # outputs: "Hello! World! "
  ```

* `print()` also auto concats multiple strings, defined without `,`

  ```python
  print("Hello"
        "World")
  # outputs: "HelloWorld"
  # multilined strings can be defined 2 ways
  var = ("Hello "
        "World!")

  var = "Hello " \
        "World!"
  ```

* NOTE: Best practice when writing python code, is to leave a newline at the end of the file
  - PyCharm will notify you of this by denoting a yellow line under your last character

<h2>DocStrings</h2>

* `Docstrings` are meant fot those who want to undestand what the program is doing without actually reading the source code
* Usually denoted with `"""`

  ```python
  def hello(name):
    """
    Return a greeting
    To the name passed to the function.
    """
    return "Hello" + name
  ```

<h2>String Manipulation</h2>

* There are multiple ways for us to manipulate the strings we provide the `print` function

  ```python
  print("Hello\nWorld")
  # \n will add a new line at the designated area
  ```

* We can also combine strings together to produce one output
 
  ```python
  # String Concatenation
  print("Hello" + " " + "World")
  # will ouput "Hello World"
  ```

<h2>Input Functions</h2>

* The `input()` allows us to recieve 'input' from the user being prompted

  ```python
  input("What is your name? ")
  # Prompt will wait for your input before completing the process
  # within the input function, we're adding the text that will be prompted to the user
  ```

* This value will entered will stored within this input to be used later

  ```python
  print("Hello " + input("What is your name: "))
  # Prompts you for your name then responds with a "Hello"
  ```

* New lines can also be added to `input()`

  ```python
  input("What is your name:\n")
  ```

* TIP: You can comment and uncomment lines of code by using `cmd + \` on mac or `ctrl + \` on windows

<h2>Length Function</h2>

* The `len()` allows us to find the length of a string

  ```python
  print(len(input("What is your name: ")))
  ```

<h2>Quit()</h2>

* Extra not, the `quit()` is used to exit out of the python terminal
