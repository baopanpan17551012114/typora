# python中字典的继承关系

```py
from collections.abc import Mapping, MutableMapping  # 可变的映射类型

# python中的字典是一种映射类型
dict_data = dict()

print(isinstance(dict_data, Mapping))
print(isinstance(dict_data, MutableMapping))
```