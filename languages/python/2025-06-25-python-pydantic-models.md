## Pydantic Models
- [Overview](#overview)
- [Getting Started](#getting-started)
- [Model Methods](#model-methods)
- [Data Validation](#data-validation)
  - [Data Conversion](#data-conversion)
- [JSON Serialization](#json-serialization)

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
      account_id: int
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
      "class_number": 12 # pydantic may cast input data to force conformation to field types, if this was 12.0, pydantic would force it to be 12
  }

  user = User(**user_data) # `**` is how we unpack dictionaries, like `*` is how we unpack lists

  # accessing attributes of the object
  print(user.name) # returns jeff
  ```

## Model Methods
* As with `lists` and `dicts`, `pydantic models` also havge their own methods that can be called
  - More information of methods can be found [here](https://docs.pydantic.dev/latest/concepts/models/#model-methods-and-properties)

  ```python
  # User Model from "Getting Started" section
  # model_validate
  user_instance = User.model_validate(user_data) # validates object against pydantic model

  # model_validate_json
  user_instance = User.model_validate(json.dumps(user_data)) # import json, same as model_validate() but passes data as json

  # model_construct
  user_instance = User.model_construct(user_data) # skips validation, assumes data is already correct and trusted, faster than simply initiating a model

  # model_dump
  print(User(user_data).model_dump()) # generates dict representation of model, with ops to include or exclude fields

  # model_dump_json()
  print(User(user_data).model_dump_json()) # generates json representation of model, with ops to include or exclude fields

  # model_copy
  updated_user = user.model_copy(update={"name": "john"}) # shallow copy of model, but can update objects, or make a deep copy of the model

  # model_json_schema
  print(User.model_schema_json()) # generates JSON schema for a model

  # model_extra
  class User(BaseModel):
      name: str # define datatypes for each object 
      email: str
      acount_id: int
      class_number: int
      model_config = ConfigDict(extra="allow") # import ConfigDict and configure Model to allow extra inputs

  data = User(name = "john", email = "john@gmail.com", acount_id = 123, class_number = 1, users = "eddy")
  print(data.model_extra) # returns dict of extra inputs

  # model_fields_sets
  print(data.model_fields_sets)# returns a set that contains fields that were explicitly provided

  # model_post_init
  class User(BaseModel):
      name: str # define datatypes for each object  
      email: str
      acount_id: int
      class_number: int
     
      def model_post_init(self, __context): # runs after initialization of model
          class_information = f"{self.name}, assigned class: {self.class_number}"

  # model_rebuild
  User.__annotations__["class_rank"] = int # add a field to a Model
  User.model_rebuild() # rebuild so the model recognizes this new field
  ```

## Data Validation
* By default, `pydantic` has data validation, to validate whether or not the type of data passed to the model matches what is defined in said model

  ```python
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
      account_id = '21343892' # here we pass in a str, python will throw a validationError
      class_number = 12
  )
  ```

* `Pydantic` also has its own built in validators, for instance we can verify if an email passed in is a valid address

  ```python
  from pydantic import BaseModel, EmailStr # import EmailStr
  class User(BaseModel):
      name: str # define datatypes for each object
      email: EmailStr # set datatype to this import
      acount_id: int
      class_number: int

  # create instance of the model
  user = User(
      name = "jeff"
      email = "jeffwashere" # this will throw a validationError
      account_id = 21343892
      class_number = 12
  )
  ```

* You can also create your own custom validators

  ```python
  from pydantic import BaseModel, EmailStr, validator # import validator
  class User(BaseModel):
      name: str 
      email: EmailStr
      acount_id: int
      class_number: int

      # Pydantic v1
      @validator("account_id") # object to validate
      def validate_account_id(self, value): # value is automatically passed by pydantic as the value of the field being validated
          # other fields can be accessed during validation
          # (self, value, values), values is a dict that contains values of fields that have already been validated before the current one
          if value <= 0:
              raise ValueError("account_id must be postive")
          return value

  # create instance of the model
  user = User(
      name = "jeff"
      email = "jeffwashere@gmail.com"
      account_id = -21343892 # setting this to a negative will hit our validator
      class_number = 12
  )
  ```
  - Access other fields

    ```python
    from typing import Any
    from pydantic import BaseModel, validator, ValidationError

    class UserModel(BaseModel):
        password1: str
        password2: str

        @validator("password2")
        def passwords_match(cls, v: str, values: dict[str, Any]) -> str:
            if "password1" in values and v != values["password1"]:
                raise ValueError("passwords do not match")
            return v

    try:
        UserModel(password1="abc123", password2="def456") # this will return a ValueError
    except ValidationError as e:
        print(e)
    ```
- ### Data Conversion

## JSON Serialization
* `Pydantic Models` also support conversions to and from `json`

  ```python 
  from pydantic import BaseModel

  class UserModel(BaseModel):
      pass1: str
      pass2: str

  user_instance = UserModel(pass1='abc', pass2='def')
  json_str = user_instance.model_dump_json() # var containing json format of model
  json_str = user_instance.model_dump_json(include={'pass1'}) # var containing json format of model, specifying objects to include
  json_str = user_instance.model_dump_json(exclude={'pass1'}) # var containing json format of model, specifying objects to exclude
  ```
