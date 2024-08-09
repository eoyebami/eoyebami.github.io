<h1>Python Random Module</h1>

* Python has something called a `random module` that allows us to generate random subset of characters
  - python has stored all of this in a module that you'll need to import in order to use

  ```python
  # importing the required python libraries
  import random
  ```

* This module implements psuedo-random generators, which uses the `Mersenne Twister` as its core generator in python
  - This produces a 52-bit precision float, but it still completely deterministic (meaning re-runing this algo with the same input will give you the same answer, though I'm not sure how often this may occur, depending on your parameters)
  - It works by initializing a state according to a seed value, then generating a random number function, pulls a single int from the predefined state, does some bit-shifting, then generates a number.

<h2>Random Module Functions</h2>

<h3>Bookkeeping Functions</h3>

```python
random.seed(10) # initializes the random number generator
# if omitted, the os system time is used
# using the same seed will generate the same number no matter what, setting this to a dynamic value is optimal
# Hence, while by default it uses the systems current time
# If your seed or state can be determined, so can your sequence of random numbers

random.getstate() # captures the internal state of the generator
random.setstate(state) # restores previously captured state
# purpose of this is if you need to reproduce the same sequence of random numbers later on
```

<h3>Byte Functions</h3>

```python
random.randbytes(n) # generates n randome bytes
# this is deterministic, so it should not be used for security purposes
```

<h3>Integer Functions</h3>

```python
random.randrange(start, stop, step) # generates a random int from range specified
# random.randrange(0, 100, 1) # from 1-100, counting by 1 (100 numbers to choose from)
# does not support floats in v3.12

random.randinit(a, b) # generates a random int from range specified
random.getrandbits(k) # returns random int in bits
```

<h3>Sequence Functions</h3>

```python
random.choice(seq) # returns a randome element from specified seq ( a list, tuple, or a range of numbers
# provide the sequence you want random module to choose from to generate a random element
# provide a list of letter, numbers, and punctuations
# choice function will grab one index, use another function to join based on a specifie length

random.choices(population, weight=None, *, cum_weights=None, k=1)
# population: the seq you want random to choose from
# weight: a list where you can weigh the possibility of each value
# cum_weights: list where you can weigh the possibility of each value, but possibility is accumulated
# k: int determining length of returned list
# stdout will be a list, use join()

# below example generates random password with letters, digits, and special characters
import random
import string

characters = string.ascii_letters + string.digits + string.punctuation
print(''.join(random.choices(characters, k=10)))

random.shuffle(x) # shuffles the sequence x in place
myList = ["apple", "banana", "orange"]
random.shuffle(myList) # shuffles list
print(myList) # print shuffled list

random.sample(population, k, *, counts=None)
# population: sequence
# k: length of sample
```

<h3>Real-valued distributions</h3>

```python
random.random() # returns float in range(0.0, 1.0)
randome.uniform(a, b) # returns float in range(a, b)
```
