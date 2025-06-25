---
title: Recursive
mathjax: true
toc: true
categories:
  - Code  
tags:
  - Python
---

A piece of code that can print itself
```python
s = 's = %r\nprint(s%%s)'
print(s%s)
```

There are two builtin functions for turning an object into a string: `str` vs. `repr`:
- `%s` will call `str`, which is supposed to be a friendly, human readable string. 
- `%r` will call `repr`, which is supposed to include detailed information about an object's contents 

`repr` will return such an expression that can be `eval` to another object that's `==` to the orignal one

```python
class Foo:

  def __init__(self, foo):
    self.foo = foo

  def __eq__(self, other):
    """Implements == """
    return self.foo == other.foo

  def __str__(self):
    class_name = self.__class__.__name__
    return "%s(%s)" % (class_name, self.foo)

  def __repr__(self):
    # if you eval the return value of this function,
    # you'll get another Foo instance that's == to self
    class_name = self.__class__.__name__
    return "%s(%r)" % (class_name, self.foo)

f = Foo('a')
print("%s, %r" % (f, f))
#output Foo(a), Foo('a')

eval(repr(f)) == f 
# True
```
