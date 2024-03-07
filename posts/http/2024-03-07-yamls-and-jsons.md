<h1>YAMLS and JSONs</h1>
* YAML files and JSON files are similar in nature and interchangeable
* First we need to understand the different components of these files

<h2>Dictionary</h2>
* `Dictionary`: are also known as `maps` that use key-value pairs
* YAML

  ```yml
  person:
    name: John Doe
    age: 30
    city: New York
    details: # map within a map
      work: Engineer
      pay: 100000
  ```

* JSON

  ```json
  {
    "person": { # map
      "details": { # map within a map
        "name": "John Doe",
        "age": 30
      },
      "address": {
        "city": "New York",
        "street": "123 Main St"
      }
    }
  }
  ```

<h2>Lists</h2>
* `Lists`: are also known as `arrays`, are used to record a sequence of items
* YAML

  ```yml
  fruits:
  - apple
  - banana
  people: # list of dictionaries
  - name: Alice
    age: 30
  - name: Ben
    age: 25
  matrix: # a matrix or nested list
  - [1, 2, 3]
  - [4, 5, 6]
  ```

* JSON

  ```json
  {
    "fruits": ["apple", "banana", "orange"],
    "people": [
      {"name": "Alice", "age": 30},
      {"name": "Bob", "age": 25},
      {"name": "Charlie", "age": 35}
    ],
    "matrix": [
      [1, 2, 3],
      [4, 5, 6],
      [7, 8, 9]
    ]
  }
  ```

<h2>Combination</h2>
* Below is an example of using both `maps` and `arrays` together
* JSON

  ```yaml
  industry: # map
    company: Apple
    location: New York
  employees: # list of maps
  - name: Alice 
    age: 30
    departments: # nested list
      - Sales
      - Marketing
  - name: Bob
    age: 35
    departments:
      - Engineering
      - Research
      - Development
  - name: Charlie
    age: 40
    departments:
      - Human Resources
```

* JSON

  ```json
  {
    "industry": {
        "company": "Apple",
        "location": "New York"
     },
    "employees": [
      {
        "name": "Alice",
        "age": 30,
        "departments": ["Sales", "Marketing"]
      },
      {
        "name": "Bob",
        "age": 35,
        "departments": ["Engineering", "Research", "Development"]
      },
      {
        "name": "Charlie",
        "age": 40,
        "departments": ["Human Resources"]
      }
    ]
  }
  ```

