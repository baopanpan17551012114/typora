1. enumerate

**enumerate()** 函数用于将一个可遍历的数据对象(如列表、元组或字符串)组合为一个索引序列，同时列出数据和数据下标，一般用在 for 循环当中。

Python 2.3. 以上版本可用，2.6 添加 start 参数。

```
enumerate(sequence, [start=0])
```

参数：

- sequence -- 一个序列、迭代器或其他支持迭代对象。
- start -- 下标起始位置。

```python
def test_enumerate():
    list_value = [1, 2, 3]
    set_value = (1, 2)
    dict_value = {"1":1, "2":2}
    for i, value in enumerate(set_value):
        print(i, value)

if __name__ == '__main__':
    test_enumerate()
```

注1:传入参数为字典时，返回key遍历



2. round

**round()** 方法返回浮点数x的四舍五入值。

参数：

- x -- 数值表达式。

- n -- 数值表达式，表示从小数点位数。

  ```python
  round(80.23456, 2)  # 80.23
  ```



3. filter

描述：

**filter()** **函数用于过滤序列**，过滤掉不符合条件的元素，返回由符合条件元素组成的新列表。

该接收两个参数，第一个为函数，第二个为序列，序列的每个元素作为参数传递给函数进行判断，然后返回 True 或 False，最后将返回 True 的元素放到新列表中。

语法：

以下是 filter() 方法的语法:

```
filter(function, iterable)
```

参数：

- function -- 判断函数。
- iterable -- 可迭代对象。

```python
#!/usr/bin/python
# -*- coding: UTF-8 -*-
 
def is_odd(n):
    return n % 2 == 1
 
newlist = filter(is_odd, [1, 2, 3, 4, 5, 6, 7, 8, 9, 10])
print(newlist)  # 输出结果 [1, 3, 5, 7, 9]
```

**注1:** *Python2.7 返回列表，Python3.x 返回迭代器对象*



4. map

**map()** 会根据提供的函数对指定序列做映射。

参数：

- function -- 函数
- iterable -- 一个或多个序列

```python
map(lambda x: x ** 2, [1, 2, 3, 4, 5])  # 使用 lambda 匿名函数
[1, 4, 9, 16, 25]

# 提供了两个列表，对相同位置的列表数据进行相加
map(lambda x, y: x + y, [1, 3, 5, 7, 9], [2, 4, 6, 8, 10])
[3, 7, 11, 15, 19]
```

注1:返回值，Python 2.x 返回列表，Python 3.x 返回迭代器。



