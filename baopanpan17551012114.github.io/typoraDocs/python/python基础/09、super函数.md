# super函数

```python
#!/usr/bin/env python
# -*- coding:utf-8 -*-

# super函数
"""
1. super函数的作用: 调用'父类'的方法
2. 什么场景下使用super函数: 在代码重用的场景下使用
3. super函数的运行过程: 正确的理解不是简单的调用一个类的父类, 而是根据一个类中的mro顺序进行查询并调用
"""
# class A:
#     def __init__(self):
#         print('A')
#
# class B(A):
#     def __init__(self):
#         print('B')
#         # A.__init__(self)
#         super().__init__()
#
#
# b = B()


# 需要创建一个类，让类具有多线程执行的特征
import threading

# 可以重用线程中定义的属性
# class MyThread(threading.Thread):
#     def __init__(self, thread_name, user):
#         self.user = user
#         # self.thread_name = thread_name
#         super().__init__(name=thread_name)

class D:
    def __init__(self):
        print('d')


class C(D):
    def __init__(self):
        print('c')
        super().__init__()


class B(D):
    def __init__(self):
        print('b')
        super().__init__()
        
class A(B, C):
    def __init__(self):
        print('a')
        super().__init__()

a = A()
print(A.__mro__)
```

