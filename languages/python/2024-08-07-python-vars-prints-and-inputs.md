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

* NOTE: Best practice when writing python code, is to leave a newline at the end of the file
  - PyCharm will notify you of this by denoting a yellow line under your last character

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

<h2>Python Variables</h2>

* Python `variables` are a way for us to store values for use later, by associating that value with a variable with a name

  ```python
  name = input("What is your name: ")
  print(name) # calling var 'name'
  ```

* When creating variables, its important to remember that the var must remain one unit, denote an `_` to separate words so that it will still remain one unit
  - You also cannot have integers be the first character in a variable
  
  ```python
  user_name = input("What is your username: ")
  ```
