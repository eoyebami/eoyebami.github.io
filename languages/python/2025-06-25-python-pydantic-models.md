## Pydantic Models
- [Overview](#overview)
- [Getting Started](#getting-started)
- [Data Validation](#data-validation)

* In python there is no need to specify a `datatype` when creating a var, its as simple as:

  ```python
  x = "string"
  y = 10
  ```

* You're able to easily define vars and python will automatically pick up the `datatype` based on the user input
  - This could lead to trouble in programs because invalid data can be passed (e.g. passing a `string` to a var thats meant to be a number `datatype`)
* Using `pydantic modeling` we can define what variables can be passed, the `datatype` of said var, the order of operation, etc
* `Pydantic` is a data validation library in python

## Getting Started

* First install the `pydantic` package

  ```python
  pip install pydantic
  ```

* From the `pydantic` package import `BaseModel` class
  - To create a `pydantic model` create a class and extend it to the `BaseModel` class

  ```python
  from pydantic import BaseModel

  # define fields to associate to this model
  class User(BaseModel):
      name: str # define datatypes for each object 
      email: str
      acount_id: int
      class_number: int

  # create instance of the model
  user = User(
      name = "jeff"
      email = "jeffwashere@gmail.com"
      account_id = 21343892
      class_number = 12
  )

  # its possible to unpack a dictionary into this Model, as long as the data is valid 
  user_data = {
      "name": "jeff",
      "email": "jeffwashere@gmail.com",
      "account_id": 21343892,
      "class_number": 12
  }

  user = User(**user_data) # `**` is how we unpack dictionaries, like `*` is how we unpack lists

  # accessing attributes of the object
  print(user.name) # returns jeff
  ```

## Data Validation
