## 一、回表查询

#### InnoDB有两大类索引：

- 聚集索引(clustered index)：叶子节点存储行记录，因此， InnoDB必须要有，且只有一个聚集索引；

- 普通索引(secondary index)：叶子节点存储主键值；

  举个栗子，不妨设有表：

  t(id PK, name KEY, sex, flag);  *id是聚集索引，name是普通索引。*

  表中有四条记录：

  1, shenjian, m, A

  3, zhangsan, m, A

  5, lisi, m, A

  9, wangwu, f, B

  ![img](https://mmbiz.qpic.cn/mmbiz_png/YrezxckhYOzNBNFMUPN4LxzHibQqicac65fmXwWV74giaiaVZBY9THnUP62l9XVyFGmn5LUg0jlGsxQjTR2ZtlxLUA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

两个B+树索引分别如上图：

（1）id为PK，聚集索引，叶子节点存储行记录；

（2）name为KEY，普通索引，叶子节点存储PK值，即id；

例如：

select * from t where name=‘lisi’;

**是如何执行的呢？**

![img](https://mmbiz.qpic.cn/mmbiz_png/YrezxckhYOzNBNFMUPN4LxzHibQqicac65ibf3L5PzLaut1zvV9G4rPr48iaOgONxm2bc1FI6AF8388xyfFTvtVbFQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

如 **粉红色** 路径，需要扫码两遍索引树：

（1）先通过普通索引定位到主键值id=5；

（2）在通过聚集索引定位到行记录；

这就是所谓的 **回表查询** ，先定位主键值，再定位行记录，它的性能较扫一遍索引树更低。



## 二、索引覆盖

**什么是索引覆盖** **(Covering index)** **？**

explain的输出结果**Extra字段为Using index**时，能够触发索引覆盖。

索引覆盖：

![image-20210709161921635](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210709161921635.png)

非索引覆盖：

![image-20210709162144714](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210709162144714.png)

只需要在一棵索引树上就能获取SQL所需的所有列数据，无需回表，速度更快。

## **三、如何实现索引覆盖？**

常见的方法是：将被查询的字段，**建立到联合索引**里去。



## **四、哪些场景可以利用索引覆盖来优化SQL？**

**场景1：全表count查询优化**

添加索引：

alter table user add key(name);

就能够利用索引覆盖提效。

**场景2：列查询回表优化**

select id,name,sex … where name=‘shenjian’;

将单列索引(name)升级为联合索引(name, sex)，即可避免回表

**场景3：分页查询**

select id,name,sex … order by name limit 500,100;

将单列索引(name)升级为联合索引(name, sex)，也可以避免回表。