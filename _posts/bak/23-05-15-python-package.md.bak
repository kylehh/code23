---
title: Create python packages Part 1
mathjax: true
toc: true
categories:
  - Code
tags:
  - Python
---

This blog records my tests on how to create a Python package
## 1. Simplest demo of creating a python package
Creating a python packages is not hard, but there may be some tricks and pitfall. 
There is one took me hours to figour it out, and I forgot about after a month time
So I decided to record it down so I don't have to research on it anymore.  

Here is the simplest demo of creating a python package called `khpack`


```python
!ls ../khpack_work/
```

    README	__init__.py  khpack  setup.py  test.py


`__init__.py` and `setup.py` are all you need to start a package. 
- `__init__.py` can be empty 
- `setup_up.py` are shown below


```python
from setuptools import setup, find_packages
  
try:
    __version__ = open("VERSION", "r").read()
except FileNotFoundError:
    __version__ = "0.0.1"

setup(
    name="khpack",
    version=__version__,
    description="kh test only",
    install_requires=['numpy'],
    include_package_data=True,
    packages=find_packages(),
    zip_safe=True
)
```

The __most__ tricky part is the actually package subfolder, you should name is the __same__ as your package name, which is `khpack`. I named it `src` before, which make `import khpack` fail even though you can see 'khpack' from `pip list`, which is quiet a headache


```python
!ls ../khpack_work/khpack
```

    __init__.py  utils.py


In side the `khpack` folder, you also just need two files.
- `__init__.py` can be empty
- `utils.py` contains the code logic


```python
def helper():
    print('Hello from khpack')
    return 123
```

Now you can build the package by running `pip install -e .`. It will create `khpack.egg-info` in current folder and `site-packages/khpack.egg-link` under the installation path. This link file only contains one line, which is pointing to `../../khpack_work` folder
