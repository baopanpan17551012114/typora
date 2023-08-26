**第一类**：**“列类型”与“where值类型”不符，不能命中索引**，会导致全表扫描(full table scan)。

![s](..\typora-user-images\image-20210713193424156.png)

**<u>为什么强制类型转换不会全表扫描</u>**？



using index ：使用覆盖索引的时候就会出现

using where：查询时没使用到索引，然后通过where条件过滤获取到所需的数据。

using index condition：在查找使用索引的情况下，需要回表去查询所需的数据

![image-20210717160206862](..\typora-user-images\image-20210717160206862.png)

using index & using where：查找使用了索引，但是需要的数据都在索引列中能找到，所以不需要回表查询数据





**第二类**：**相join的两个表的字符编码不同，不能命中索引**，会导致笛卡尔积的循环计算（nested loop）。

![image-20210713193805414](..\typora-user-images\image-20210713193805414.png)

