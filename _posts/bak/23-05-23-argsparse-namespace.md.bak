---
title: argparse and Namespace
mathjax: true
toc: true
categories:
  - Code
tags:
  - Python 
---

Learn something really trival today, but kind of interesting and useful, which is related to argparse library
We all use argparse for argument parsing. The standard usage is shown below:

```python
import argparse

def parse_args():
    parser = argparse.ArgumentParser()
    parser.add_argument(
        "-a",
        "--asyn",
        action = 'store_true',
        help="Async optional",
    )
    parser.add_argument(
        "-b",
        "--batch-size",
        type=int,
        default=64,
        metavar="N",
        help="input batch size for training (default: 64)",
    )
    parser.add_argument('-c','--con', action='store_const', const=123)
    #return parser.parse_args()
    
    #For testing purpose only
    #The parse_args() take a List instead of Str as input!
    return parser.parse_args('--asyn -b 5 -c'.split())

args = parse_args()
print(type(args), args.asyn, args.batch_size, args.con)
```

    <class 'argparse.Namespace'> True 5 123


Couple of things amazed me when I first time looked at this library, is that it can automatecally change `-` to `_`, like the flag `--batch-size` changed to `batch_size` in the final args namespace.

Oh, namespace. yes, the `args` is **Namespace** type variable, so you can directly use `args.batch_size`, which is very convenient!

But, what if the following program ask for a dict type as input, which is very common. Or in reverse, if I have a dict type, how can I convert it to a Namespace?  

Let's solve them one by one:
## 1. converting **Namespace** to **Dictionary**


```python
def dummy_func(args):
    args_dict = vars(args)
    batch_size = args_dict['batch_size']
    print('Namespace is converting to Dict',batch_size)
    
dummy_func(args)
```

    Namespace is converting to Dict 5


You see, very easy. Just use the magic of `vars()`... Actually I know very few about `vars`, will check out more later

## 2. converting **Dictionary** to **Namespace**


```python
def D2N(dic_input):
    # Method 1, by Namespace class from argparse
    from argparse import Namespace
    args = Namespace(**dic_input)
    print('Dict is converting to Namespace by method 1:', args.batch_size)
    
    # Method 2, define your own Namespace class
    class Namespace_:
        def __init__(self, adict):
            self.__dict__.update(adict)
    args_ = Namespace_(dic_input)
    print('Dict is converting to Namespace by method 2:', args_.batch_size)
    
D2N({'batch_size': 5})
```

    Dict is converting to Namespace by method 1: 5
    Dict is converting to Namespace by method 2: 5


Nice and neat! Both methods have some interesting points:
Method 1, use `**` to unpack the dictionary
Method 2, use `__dict__`, which I know as little as `vars`. will check it out

I know, it's sth really trivial. But I would forgot in 2 days so I decided to write it down. 

