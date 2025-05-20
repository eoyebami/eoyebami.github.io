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

* Its possible to swap values of indexes in a list

```python
list[0], list[1] = list[1], list[0] # swaps first and second element in a list with each other
# you can increment on this method and swap as many values as you'd like
```

* Iterating over a list

```python 
total_ages = 0
for age in list:
   total_ages += age

# get average age
average_age = total_ages / len(list)
print(average_age)
```

<h2>List Methods</h2>

* Methods behave like built in python functions but they serve a different purpose. While a function acts on its own, a method is owned by the data that it works for

```python
list = [1, 2, 3, 4]
list.append(5) # appends a new item at the end of a list
print(list) # [1, 2, 3, 4, 5]

list.insert(2, 2.5) # inserts a new item at the specified index in a list, first arg is the index and second arg is the value
print(list) # [1, 2, 2.5, 3, 4]

list_copy = list.copy() # creates a copy of a list

list.clear() # clears all item in a list

list.count(2) # counts the occurrence of a specified element in a list

list.extend([5, 6]) # extends the list by adding elements from another list
print(list) # [1, 2, 3, 4, 5, 6]

list.index(2) # finds the index of a specified element in a list
print(list.index(2)) # 1

list.pop() # removes and returns the last element in the list, in this case it will return 4
print(list) # [1, 2, 3]

list.reverse() # will reverse the order of the list

list.sort() # will sort the elements by key and in ascending or descending
list.sort(reverse=True) # will sort in descending order
list.sort(key=len) # will sort by length
# key can also be set to a function that will return something that will allow python to sort order of list
# for strings, capital letters are order first in ascending order, set key to str.lower to convert to lower case before sorting 

list.remove(2) # removes the first occurrence of the element with the specified value
```

<h2>Slicing Lists</h2>

* NOTE: when you create a list and assign it to a var, python just assigns the reference to the address where that list is stored in memory
  - This means if you create another var to equal the var of that list, should you change the original list, the new vars value will also changes
  - To avoid this, we must slice the list or run a list.copy() when assigning the list value to a new var

