<h1>Dictionaries</h1>

* A `dictionary` in python consists of keys and values to create key/value pairs

```python
students_classes = {
  "mark": "class-a",
  "luke": "class-b",
  "matt": "class-c",
  "susan": "class-d",
}
# last item in a array or map usually end in a `,`
```

* In order to retrieve a value of a key in a map, you would just reference the name of that key in brackets

```python
students_classes["luke"] # this would return the value "class-b"
```

<h2>Dictionary Methods</h2>

* There are a couple methods you can use to better manipulate maps in python

```python
students_classes.clear() # removes all elements in a map

students_classes.copy() # creates a copy of a map, similar to list.copy() for lists

students_classes.fromkeys() # returns a map with specified key/value pair
keys = ["a", "b", "c"]
value = 1
students_classes.fromkeys(keys, value) # will return {'a': 1, 'b': 1, 'c': 1}
# if no value is specified, value will default to 'None'
# values can be lists which are mutable
value = [1]
students_classes.fromkeys(keys, value) # will return {'a': [1], 'b': [1], 'c': [1]}
values.append(2) # since lists point to a location of the value in memory, this will also update the dict
# will return {'a': [1 ,2], 'b': [1, 2], 'c': [1, 2]}
# to prevent this update, you could just slice the list, or use the copy method
# for loops would also work to make your own dict from keys
dict_creation = { key: list(value) for key in keys }

students_classes.get(keyname, value) # returns a value of a specified key
students_classes.get("mark") # returns "class-a"
students_classes.get("mark", "class unknown") # returns "class-a" or "class unknown" if specified key does not exist

students_classes.items() # returns a list containing a tuple for each key/value pair (iterable)
# returns dict_items([('mark', 'class-a'), ('luke', 'class-b'), ('matt', 'class-c'), ('susan', 'class-d')]) 
# iterate over items() 
for key, value in students_classes.items():
  print(f"key is {key}, value is {value}")

students_classes.keys() # returns list of keys in dict
# dict_keys(['mark', 'luke', 'matt', 'susan'])
# make a list [keys for keys in students_classes.keys()]

students_classes.pop(keyname, defaultvalue) # removes the specified item from a dict
students_classes.pop("mark") # removes mark from dict, outputs value of specified key
students_classes.pop("mark", "remove") # removes mark from dict, outputs "remove" if key does not exist in dict otherwise error will be raises

students_classes.popitem() # removes last item from dict, outputs tuple of removed item

students_classes.setdefault(keyname, value) # returns the value of specified key, if it does not exist, given value is set as default for that key
students_classes.setdefault("mark", "class-z")

students_classes.update(iterable) # inserts items to dict, can be another dict or an iterable of key/value pairs
students_classes.update({"mark": "class-z"}) 
students_classes.update({key: value for key in keys}) # iterates over to make a dict then updates using method
# can also be done by calling directly 
students_classes["nathan"] = "class-g"

students_classes.values() # returns a list of all values in a dict
```

<h2>Multidimensional Dicts</h2>

* Multidimensional Dicts can also be created just like multidimensional lists 

```python
my_2d_dict = {
    'row1': {'col1': 1, 'col2': 2},
    'row2': {'col1': 3, 'col2': 4}
}

# calling object in dict
my_2d_dict["row1"]["col1"] # will return 1

# iterating over 2d dict
for row_key, row_dict in my_2d_dict.items():
    print(f"Row: {row_key}") # prints "row1" and "row2"
    for col_key, value in row_dict.items():
        print(f"  Column: {col_key}, Value: {value}")

# create multidimensional dict dynamically
keys = ["a", "b", "c"]
value = 1
print({key: {"test": value} for key in keys})

# option 2
keys = ["a", "b", "c"]
values = [1, 2, 3]

dict = {}
for i in range(len(keys)):
    dict[[keys[i]] = values[i]
print(dict) # returns {'a': 1, 'b': 2, 'c': 3}

# option 3
keys = ["a", "b", "c"]
values = [1, 2, 3]

dict = {keys[i]: values[i] for i in range(len(keys))}
```

