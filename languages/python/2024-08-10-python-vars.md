<h1>Python Variables</h1>

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

* You can't save certain words as vars, as these are `reserved words`:

  ```
  # reserved words
  False await else import pass
  None break except in raise
  True class finally is return
  and continue for lambda try
  as def from nonlocal while
  assert del global not with
  async elif if or yield
  ```
