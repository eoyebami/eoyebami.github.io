<h1>Python Lists</h1>

* In `python` we can create data structures(which is a way of organizing data), its a sequence

```python
countries = ["USA", "Canada", "india"]
countries[0] = "UK"
# using indexes within an list you can extract or modify to that list
# the above code changes the index of 0 which is "USA" to "UK"

print(countries[0]) 
# this will return the first index on that list

print(countries[-1]) 
# negative indexes can also be specified to retrieve values from list in reverse order
```

* The built in `len()` allows us to get the length of that list as an integer

```python 
len(countries)
# this will return a value of 3
```

* The `del` statement allows us to remove items at a given position in a list

```python
del countries[0]
# deletes the first index in the countries list

del countries[0:2]
# deletes the first and second index
```

<h2>List Methods</h2>
