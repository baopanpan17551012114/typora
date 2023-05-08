DataFrame中的一行,其中的字段可以像属性一样访问。

Row可以用来通过使用命名参数来创建一个行对象，字段将按名称排序。



```python
from pyspark.sql import Row

row = Row(name="name", age="age")  # 继承自tuple，不能有重复元素
row_dict = row.asDict()
name = row.__getitem__("name")
name = row["name"]
print(name)
```

