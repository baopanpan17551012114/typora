python内置装饰器

1. ```python
   @abc.abstractmethod
   用来虚化方法的装饰器，父类使用了@abc.abstractmethod装饰器，而子类没有将每一个父类重写都会报错,抽象基类也是这个原理
   ```

2. ```python
   @staticmethod
   效果同java中静态方法，可以通过实例调用，也可以通过类调用
   
   #!/usr/bin/python
   # -*- coding: UTF-8 -*-
   class C(object):
       @staticmethod
       def f():
           print('runoob');
   C.f();          # 静态方法无需实例化
   cobj = C()
   cobj.f()        # 也可以实例化后调用
   ```

3. ```python
   @classmethod
   classmethod 修饰符对应的函数不需要实例化，不需要 self 参数，但第一个参数需要是表示自身类的 cls 参数，可以来调用类的属性，类的方法，实例化对象等。
   
   class A(object):
       bar = 1
       
       def func1(self):
           print ('foo')
   
       @classmethod
       def func2(cls):
           print ('func2')
           print (cls.bar)
           cls().func1()  # 调用 foo 方法
           print(cls.__name__)
   
   def test_method():
       A.func2()  # 不需要实例化
   
   if __name__ == '__main__':
       test_method()
   ```

   