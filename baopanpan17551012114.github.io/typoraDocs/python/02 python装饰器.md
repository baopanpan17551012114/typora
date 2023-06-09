简言之，[python](https://so.csdn.net/so/search?from=pc_blog_highlight&q=python)装饰器就是用于**拓展原来函数功能的一种函数**，这个函数的特殊之处在于它的返回值也是一个函数，使用python装饰器的好处就是在不用更改原函数的代码前提下给函数增加新的功能。

一般而言，我们要想拓展原来函数代码，最直接的办法就是侵入代码里面修改，例如：

```python
import time
def func():
    print("hello")
    time.sleep(1)
    print("world")
```

这是我们最原始的的一个函数，然后我们试图记录下这个函数执行的总时间，那最简单的做法就是：

```python
#原始侵入，篡改原函数
import time
def func():
    startTime = time.time()
    print("hello")
    time.sleep(1)
    print("world")
    endTime = time.time()
    msecs = (endTime - startTime)*1000
    print("time is %d ms" %msecs)
```

但是如果你的Boss在公司里面和你说：“小祁，这段代码是我们公司的核心代码，你不能直接去改我们的核心代码。”

那该怎么办呢，我们仿照装饰器先自己试着写一下(***<u>其实是按照回调函数的思路</u>***)：

```python
#避免直接侵入原函数修改，但是生效需要再次执行函数
import time

def deco(func):
    startTime = time.time()
    func()
    endTime = time.time()
    msecs = (endTime - startTime)*1000
    print("time is %d ms" %msecs)

def func():
    print("hello")
    time.sleep(1)
    print("world")

if __name__ == '__main__':
    f = func
    deco(f)#只有把func()或者f()作为参数执行，新加入功能才会生效
    print("f.__name__ is",f.__name__)#f的name就是func
```

这里我们定义了一个函数deco，它的参数是一个函数，然后给这个函数嵌入了计时功能。然后你可以拍着胸脯对老板说，看吧，不用动你原来的代码，我照样拓展了它的函数功能。

然后你的老板有对你说：“小祁，我们公司核心代码区域有一千万个func()函数，从func01()到func1kw(),按你的方案，想要拓展这一千万个函数功能，就是要执行一千万次deco()函数，这可不行呀，我心疼我的机器。”

好了，你终于受够你老板了，准备辞职了，然后你无意间听到了装饰器这个神器，突然发现能满足你的要求了。
我们先实现一个最简陋的装饰器，不使用任何语法糖和高级语法，看看装饰器最原始的面貌：

```python
#既不需要侵入，也不需要函数重复执行
import time

def deco(func):
    def wrapper():
        startTime = time.time()
        func()
        endTime = time.time()
        msecs = (endTime - startTime)*1000
        print("time is %d ms" %msecs)
    return wrapper

@deco
def func():
    print("hello")
    time.sleep(1)
    print("world")

if __name__ == '__main__':
    f = func #这里f被赋值为func，执行f()就是执行func()
    f()
```


这里的deco函数就是最原始的装饰器，它的参数是一个函数，然后返回值也是一个函数。其中作为参数的这个函数func()就在返回函数wrapper()的内部执行。然后在函数func()前面加上@deco，func()函数就相当于被注入了计时功能，现在只要调用func()，它就已经变身为“新的功能更多”的函数了。
所以这里装饰器就像一个注入符号：有了它，拓展了原来函数的功能既不需要侵入函数内更改代码，也不需要重复执行原函数。

```python
#带有参数的装饰器
import time

def deco(func):
    def wrapper(a,b):
        startTime = time.time()
        func(a,b)
        endTime = time.time()
        msecs = (endTime - startTime)*1000
        print("time is %d ms" %msecs)
    return wrapper

@deco
def func(a,b):
    print("hello，here is a func for add :")
    time.sleep(1)
    print("result is %d" %(a+b))

if __name__ == '__main__':
    f = func
    f(3,4)
    #func()
```

然后你满足了Boss的要求后，Boss又说：“小祁，我让你拓展的函数好多可是有参数的呀，有的参数还是个数不定的那种，你的装饰器搞的定不？”然后你嘿嘿一笑，深藏功与名！

```python
#带有不定参数的装饰器
import time

def deco(func):
    def wrapper(*args, **kwargs):
        startTime = time.time()
        func(*args, **kwargs)
        endTime = time.time()
        msecs = (endTime - startTime)*1000
        print("time is %d ms" %msecs)
    return wrapper
  
@deco
def func(a,b):
    print("hello，here is a func for add :")
    time.sleep(1)
    print("result is %d" %(a+b))

@deco
def func2(a,b,c):
    print("hello，here is a func for add :")
    time.sleep(1)
    print("result is %d" %(a+b+c))

if __name__ == '__main__':
    f = func
    func2(3,4,5)
    f(3,4)
    #func()
```

最后，你的老板说：“可以的，小祁，我这里一个函数需要加入很多功能，一个装饰器怕是搞不定，装饰器能支持多个嘛"
最后你就把这段代码丢给了他：

```python
#多个装饰器

import time

def deco01(func):
    def wrapper(*args, **kwargs):
        print("this is deco01")
        startTime = time.time()
        func(*args, **kwargs)
        endTime = time.time()
        msecs = (endTime - startTime)*1000
        print("time is %d ms" %msecs)
        print("deco01 end here")
    return wrapper
  
def deco02(func):
    def wrapper(*args, **kwargs):
        print("this is deco02")
        func(*args, **kwargs)
				print("deco02 end here")
		return wrapper
  
@deco01
@deco02
def func(a,b):
    print("hello，here is a func for add :")
    time.sleep(1)
    print("result is %d" %(a+b))

if __name__ == '__main__':
    f = func
    f(3,4)
    #func()

'''
this is deco01
this is deco02
hello，here is a func for add :
result is 7
deco02 end here
time is 1003 ms
deco01 end here
'''
```


多个装饰器执行的顺序就是从最后一个装饰器开始，执行到第一个装饰器，再执行函数本身。

盗用评论里面一位童鞋的例子：

```python
def dec1(func):  
    print("1111")  
    def one():  
        print("2222")  
        func()  
        print("3333")  
    return one  

def dec2(func):  
    print("aaaa")  
    def two():  
        print("bbbb")  
        func()  
        print("cccc")  
    return two  

@dec1  
@dec2  
def test():  
    print("test test")  

test()  
```


输出：

```python
aaaa  
1111  
2222  
bbbb  
test test  
cccc  
3333
```

装饰器的外函数和内函数之间的语句是没有装饰到目标函数上的，而是在装载装饰器时的附加操作。

17~20行是装载装饰器的过程，相当于执行了test=dect1(dect2(test))，此时先执行dect2(test)，结果是输出aaaa、将func指向函数test、并返回函数two，然后执行dect1(two)，结果是输出1111、将func指向函数two、并返回函数one，然后进行赋值。

用函数替代了函数名test。 22行则是实际调用被装载的函数，这时实际上执行的是函数one，运行到func()时执行函数two，再运行到func()时执行未修饰的函数test。



```python
from core import app
class Task(object):
    """
    装饰器的扩展功能部分，消息队列相关
    """
    def __init__(self, func, producer=None, consumer=None,
                 topic=None, async=True):
        self.func = func          # 被装饰的函数
        self.producer = producer  # 生产者
        self.consumer = consumer  # 消费者
        self.topic = topic        # 消息队列中主题
        self.async = async        # 同/异步标识

    def delay(self, **kwargs):
        self.producer.send(       # 生产数据，放入队列
            topic=self.topic,     # 指定topic
            msg=kwargs,           # 要发送的数据通过入参获得
        )

    def as_task(self):
        def task(body, meta):
            with app.app_context():
        return task

    def consume(self):
        self.consumer.consume(        # 从队列中获取数据
            queue=self.topic,         # 指定topic
            listener=self.as_task(),
            async=self.async,
        )

def task(consumer=None, producer=None, topic=None, async=True):
    """
    消息队列任务装饰器
    """
    def inner_task(func):
        return Task(
            func=func,
            consumer=consumer,
            producer=producer,
            topic=topic,
            async=async,
        )
    return inner_task

@task(consumer=mq_consumer, producer=mq_producer, topic='process_inshop_task_pool')
def process_pool_match_task(**kwargs):
  	# 增加消息队列功能
    try:
        logging.info("######producer#######")
    except Exception as e:
        print(e)
```

