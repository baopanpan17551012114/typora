



# Set difference() 方法

描述：

difference() 方法用于返回集合的差集，即返回的集合元素包含在第一个集合中，但不包含在第二个集合(方法的参数)中。

语法：

difference() 方法语法：

```
set.difference(set)
```

```python
x = {"apple", "banana", "cherry"}
y = {"google", "microsoft", "apple"}
z = x.difference(y) 
print(z)
```



# Set intersection() 方法

描述：

intersection() 方法用于返回两个或更多集合中都包含的元素，即交集。

语法：

intersection() 方法语法：

```
set.intersection(set1, set2 ... etc)
```

参数：

- set1 -- 必需，要查找相同元素的集合
- set2 -- 可选，其他要查找相同元素的集合，可以多个，多个使用逗号 , 隔开

```python
x = {"apple", "banana", "cherry"}
y = {"google", "runoob", "apple"}
z = x.intersection(y) 
print(z)
```

