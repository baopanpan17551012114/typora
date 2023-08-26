# `*args` 和 `**kwargs`的用法

https://blog.csdn.net/GODSuner/article/details/117961990

```python
# 函数参数中*args 和 **kwargs
"""
*args 表示任何多个无名参数， 他本质上是一个 tuple
** kwargs 表示关键字参数， 它本质上是一个 dict
"""


# *args
def print_func(x, y, *args):
    print(type(x))
    print(x)
    print(y)
    print(type(args))
    print(args)
print_func(1,2,'python希望社',[])


# **kwargs
def print_func(**kwargs):
    print(type(kwargs))
    print(kwargs)
print_func(a=1, b=2, c='呵呵哒', d=[])


# arg,*args,**kwargs 作为函数参数的顺序
def print_func(x, *args, **kwargs):
    print(x)
    print(args)
    print(kwargs)

print_func(1, 2, 3, 4, y=1, a=2, b=3, c=4)
```

