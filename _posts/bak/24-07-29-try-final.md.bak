---
title: Elo ranking and Bradly-Terry
mathjax: true
toc: true
categories:
  - Study
tags:
  - Math
---

## 1. try/catch/else/finally
Encountered an interesting piece of code for try/catch
```python
def no_env_var(var: str):
    try:
        # If you have this var, remove it
        if val := os.environ.get(var, None):
            del os.environ[var]
        yield
    finally:
        # Restore the var after this function
        if val:
            print("1", os.environ.get(var, None))
            os.environ[var] = val
            print("2", os.environ.get(var, None))

os.environ["abc"] = "123"
print("3",os.environ.get("abc",None))
with no_env_var("abc"):
    print("4",os.environ.get("abc",None))
print("5",os.environ.get("abc",None))
```
Couple of things to notice:  
1. `finally` will always execute
2. `try/catch/else` is for exception case and no exception case.
3. The `field` in the code will make the `finally` execute after the `field` finish

So the output is 
```python
3 123    # Set inital var 
4 None   # inside the no_env_var scope
1 None   # In finally
2 123    # In finally, restore the var
5 123    # outside finally
```
## 2 Python Methods
  0. normal method in a class takes `self` argument implicitly
  1. `staticmethod` does NOT take `self` argument. Or you can add it explicitly, as a normal function
  2. `classmethold` takes `cls` argument implicityly

  ```python
    class Pizza(object):
        def __init__(self, size):
            self.size = size
        def get_size(self):
            return self.size
        @staticmethod
        def get_shape():
            return 'round'
        @classmethod
        def get_name(cls):
            return cls
    Pizza.get_size
    #<function __main__.Pizza.get_size(self)>
    Pizza.get_shape
    #<function __main__.Pizza.get_shape()>
    Pizza.get_name
    #<bound method Pizza.get_name of <class '__main__.Pizza'>>
  ```

  3. Normal method has to run it with an object
  ```python
  Pizza.get_size()
  #TypeError: Pizza.get_size() missing 1 required positional argument: 'self'
  Pizza(10).get_size()
  # 10
  Pizza.get_size(Pizza(10))
  # This is sth trick to implement. Also return 10 
  ```
  4. `staticmethod` and `classmethod` can run with the class or an object
  ```python
  Pizza.get_shape()
  # round
  Pizza(10).get_shape()
  # round
  Pizza.get_name()
  # __main__.Pizza
  Pizza(10).get_name()
  # __main__.Pizza
  ```
  5. `abstractmethod` is used in base class and force to be modified. 
  ```python
    import abc
 
  class BasePizza(object):
    @abc.abstractmethod
    def get_radius(self):
         """Method that should do something."""
    def get_size(self):
      # will not raise unless call get_size 
      raise NotImplementedError
  BasePizza()
  # Will error even without calling get_radius
  #TypeError: Can't instantiate abstract class BasePizza with abstract methods get_radius
  ```