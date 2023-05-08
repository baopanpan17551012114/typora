# Hive中复杂数据类型Map常用方法介绍

https://blog.csdn.net/m0_37773338/article/details/86373370

1. size(Map)函数：可得map的长度。返回值类型：int

select size(t.params);

2. map_keys(Map)函数：可得map中所有的key;  返回值类型: array

select map_keys(t.params);

3. map_values(Map)函数：可得map中所有的value; 返回值类型: array

select map_value(t.params);
4. 判断map中是否包含某个key值：

select array_contains(map_keys(t.params),'k0');

5. 在k-v对中，若value有多个值的情况，如 {'k1':'01,02,03'} ，如果要用 'k1' 中 '02'作为过滤条件，则语句如下：

    （这里用到split来处理）

select * from t  where split(t.params['k1'],',')[1]
6. 如果过滤条件为：k2的值必须为'45'开头，则语句如下：

  （这里用到substr方法来处理，这里注明一下，1和2分别表示起始位置和长度）

select * 
from t 
where substr(t.params['k2'],1,2) = '45'



#### 通常要搭配str_to_map，因为map或者json(map)在hive都为string

### str_to_map(字符串参数, 分隔符1, 分隔符2)

使用两个分隔符将文本拆分为键值对。

分隔符1将文本分成K-V对，分隔符2分割每个K-V对。对于分隔符1默认分隔符是 **','**，对于分隔符2默认分隔符是 **'='**。