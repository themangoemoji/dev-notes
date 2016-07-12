# Python

> The Zen of Python, by Tim Peters 

>Beautiful is better than ugly.
Explicit is better than implicit.
Simple is better than complex.
Complex is better than complicated.
Flat is better than nested.
Sparse is better than dense.
Readability counts.
Special cases aren't special enough to break the rules.
Although practicality beats purity.
Errors should never pass silently.
Unless explicitly silenced.
In the face of ambiguity, refuse the temptation to guess.
There should be one-- and preferably only one --obvious way to do it.
Although that way may not be obvious at first unless you're Dutch.
Now is better than never.
Although never is often better than *right* now.
If the implementation is hard to explain, it's a bad idea.
If the implementation is easy to explain, it may be a good idea.
Namespaces are one honking great idea -- let's do more of those!

## Virtual env

This is included in python 3, not in python 2.7

To create one:

`python3 venv env`

This creates a virtualenv with the name venv

To create a venv with a different version:

`virtualenv -p /usr/bin/python<version> <env-name>`

### Parsing Args

Python 3:

```python
import argparse
parser = argparse.ArgumentParser()
args = parser.parse_args()
```

### Assignment

In python, you dont need a swap function. You can use:

`a, b = b, a`

## Abstract Base Classes

* [ABC Blog Post](https://dbader.org/blog/abstract-base-classes-in-python)

