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



https://blog.csdn.net/qq_45611850/article/details/119247655

前言
最近学习python中的函数的收集参数时，遇到了两个特殊的参数符号：*和**
下面简单讨论下这两个符号的用法。

一、用法一：解包（Unpacking）
1.*对容器类型list、tuple、dict、set解包
简单来说就是*可以把上面三种数据类型中的每个元素都
扒掉外衣（有点粗鲁，但实际就是这样）

让我们来看下扒掉的效果：

>>> list1 = [1,2,3]
>>> print(*list1)
>>> 1 2 3
>>> 1
>>> 2
>>> 3
>>> 扒掉外衣接下来我们可以对它进行一些封装的操作，把它们封装成其他数据类型，像是dict、list，我们来试试：

list1 = [1,2,3]
tup1 = (1,2,3)
set1 = {1,2,3}
dict1 = {'name':'Mogu134','age':19}

print([*list1]) # [1, 2, 3]
print({*list1}) # {1, 2, 3}
print(*tup1) # 1 2 3
print({*tup1}) # {1, 2, 3}
print([*set1]) # [1, 2, 3]
print(*dict1) # name age
1
2
3
4
5
6
7
8
9
10
11
'
运行运行
2.**对容器dict解包
同样的，我们来看对dict解包会发生什么：

>>> print(**dict1)
>>> Traceback (most recent call last):
>>> File "<pyshell#54>", line 1, in <module>
>>> print(**dict1)
>>> TypeError: 'name' is an invalid keyword argument for print()
>>> 1
>>> 2
>>> 3
>>> 4
>>> 5
>>> 这是个错误演示，因为print()函数没有关键字参数name、age所以报错，但是它返回的值实际上是这样的：

name = 'Mogu134',age = 19
1
那字典解包该怎么用呢，下面通过两个小栗子来说明：

# 案例一 解包的元素作为参数传给定义的函数
>>> def myfun(name,age):
>>> print(name,age)
>>> myfun(**dict1)
>>> Mogu134 19

# 案例二 解包两个字典合为一个字典
>>> dict2 = {'nationality':'China','hobby':'eating'}
>>> print({**dict1,**dict2})
>>> {'name': 'Mogu134', 'age': 19, 'nationality': 'China', 'hobby': 'eating'}
>>> 1
>>> 2
>>> 3
>>> 4
>>> 5
>>> 6
>>> 7
>>> 8
>>> 9
>>> 10
>>> 二、用法二：收集参数
>>> 1.*收集元组
>>> *args收集参数接受任意数量的位置实参，并把所有收集到的实参存入args元组中：

>>> def test(*args):
>>> print(args)	
>>> test('晚上','操场','团建','班导','星空','草地','路灯','快速路','飞机')
>>> ('晚上', '操场', '团建', '班导', '星空', '草地', '路灯', '快速路', '飞机')
>>> 1
>>> 2
>>> 3
>>> 4
>>> 2.**收集字典
>>> **kwargs收集参数接受任意数量的关键字实参，并把所有收集到的实参存入kwargs字典中：

>>> def test(**kwargs):
>>> print(kwargs)	
>>> test(time='晚上',place='操场',activity='团建')
>>> {'time': '晚上', 'place': '操场', 'activity': '团建'}
>>> 1
>>> 2
>>> 3
>>> 4
>>> 总结
>>> 以上就是今天要讲的内容，本文仅仅简单介绍了*和**的两种用法：给某些容器类型解包和用作收集参数，而python的解包符提供了更多功能和操作，更多用法等待大家探索~~
