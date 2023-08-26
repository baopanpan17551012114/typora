# isinstance 和 type

```python
#!/usr/bin/env python
# -*- coding:utf-8 -*-

# isinstance 和 type

class A:
    pass

class B(A):
    pass

b = B()

# 判断传入的对象是否是指定的类型
# 判断的对象也参考类的继承关系
print(isinstance(b, B))
print(isinstance(b, A))

#  == 是判断两个值是否相等
print(type(b))
print(type(b) == B)
print(type(b) == A)

# is判断的是内存地址
print(type(b) is B)
print(type(b) is A)

"""
在判断对象的类型时尽量优先使用isinstance
"""

```