## Python Virtual Environments

* Python virtual environments are isolated directories that hold a specific python interpreter (a version of python)
  - They have their own set of installed python packages and are complete abstract from the system-wide python installation
* This is basically the docker version of python, isolated systems, isolated packages, numerous use cases without interference 

```python
python -m venv <name-of-venve>
python -m venv testing # this wil create a virtual environment within the pwd
```

* With the directory created when you create you're python environment, you'll see the python binary which will be the interpreter for that venv

* By default when creating a `venv` the global python installation will be the python intepreter for that venv, but this can be changed

```python
python3.12 -m venv testing # creating a virtual environment within the pwd using python version 3.12
```

* You can then initialize the venv and install python packages specific to that virtual environment

```python
# activate venv
source testing/bin/activate # activates the virtual environment

# verify version of venv
python --version

# install packages
python -m pip install asyncio
pip install asyncio # will also work
```

* Deactivate a virtual environment

```python
deactivate # simply run the deactivate statement in your terminal
```
