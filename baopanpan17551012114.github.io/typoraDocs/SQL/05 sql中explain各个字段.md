# SQL执行计划

https://www.yuque.com/yinjianwei/vyrvkf/wgdwll



![image-20210715171342518](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210715171342518.png)

**id：SELECT识别符。这是SELECT的查询[序列号](https://www.baidu.com/s?wd=序列号&tn=24004469_oem_dg&rsv_dl=gh_pl_sl_csd)。**

**select_type：SELECT类型，一般没用。**

**table：表名**

**type：联接类型。由上至下，效率越来越高**：

​	all：全表扫描。sql语句处于一种最原生的状态，有很大的优化空间。

​	index：索引被扫描。该联接类型与ALL相同，除了只有索引树被扫描。

​	range：有范围的索引扫描，相对于index的全索引扫描，它有范围限制，因此要优于index。

​	ref：查找条件列使用了索引而且不为主键和unique。其实，意思就是虽然使用了索引，但该索引列的值并不唯一，有重复。

​	eq_ref：ref相比，这种类型的查找结果集只有一个（主键或者唯一性索引才能保证）。

​	const：const用于用常数值比较PRIMARY KEY或UNIQUE索引的所有部分时。

​	system：表仅有一行(=系统表)。这是const联接类型的一个特例。

​	NULL：MySQL不访问任何表或索引，直接返回结果。

**possible_keys：可能用到的索引，多了需要优化。**

**key：key列显示MySQL实际决定使用的键(索引)。如果没有选择索引，键是NULL。**

**key_len：key_len列显示MySQL决定使用的键长度。**

**ref：ref列显示使用哪个列或常数与key一起从表中选择行。**

**rows：扫描行数。rows列显示MySQL认为它执行查询时必须检查的行数。**

### filtered 

表示返回结果的行数占需读取行数的百分比， filtered 的值越大越好。



**请拿起 explain 武器，如果你看到以下现象，请优化：**

  1）出现了Using temporary，一般临时表排序，需要优化

  2）出现了Using filesort，排序时无法使用到索引时，就会出现这个

  3）rows过多，或者几乎是全表的记录数

  4）key 是 (NULL)

  5）possible_keys 出现过多（待选）索引



id：select 查询的序列号；
select_type：查询的类型，主要是用于区分普通查询、联合查询、子查询等；
table：查询涉及到的表；
partitions：对于分区表，显示查询的分区ID，对于非分区表，显示为NULL；
type：访问类型，SQL 查询优化中一个重要指标；
possible_keys：查询过程中有可能用到的索引；
key：实际使用的索引，如果为 NULL ，则没有使用索引。
key_len：使用索引的字节长度；
ref：等值查询会显示const，连接查询的话被驱动表此处显示驱动表的join列；
rows：根据表统计信息或者索引选用情况，大致估算出找到所需的记录所需要读取的行数；
filtered：表示返回结果的行数占需读取行数的百分比， filtered 的值越大越好；
Extra：不适合在其他字段中显示，但是十分重要的额外信息；



# **explain结果中的type字段代表什么意思？**

type，**连接类型**(the join type)。它描述了找到所需数据使用的扫描方式。

最为常见的扫描方式有：

- **system**：系统表，少量数据，往往不需要进行磁盘IO；
- **const**：常量连接；
- **eq_ref**：主键索引(primary key)或者非空唯一索引(unique not null)等值扫描；
- **ref**：非主键非唯一索引等值扫描；
- **range**：范围扫描；
- **index**：索引树扫描；
- **ALL**：全表扫描(full table scan)；

上面各类扫描方式**由快到慢**：

system > const > eq_ref > ref > range > index > ALL

**一、system**

数据已经加载到内存里，不需要进行磁盘IO。

内层嵌套(const)返回了一个临时表，外层嵌套从临时表查询，其扫描类型也是system，也不需要走磁盘IO，速度超快。

（navicat中内层嵌套为什么不显示system，直接不显示了）

**二、const**

const扫描的条件为：

（1）命中主键(primary key)或者唯一(unique)索引；

（2）被连接的部分是一个常量(const)值；

![image-20210713182417972](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210713182417972.png)

**三、eq_ref**

eq_ref扫描的条件为，对于前表的每一行(row)，后表只有一行被扫描。

再细化一点：

（1）join查询；

（2）命中主键(primary key)或者非空唯一(unique not null)索引；

（3）等值连接；

这类扫描的速度也异常之快。

**四、ref**

eq_ref降级为ref：对于前表的每一行(row)，后表可能**有多于一行的数据被扫描**；

const降级为ref：常量的连接查询，因为**有多于一行的数据被扫描**；

示例：UNIQUE KEY `pid_goodsid` (`pid`,`goodsid`) USING BTREE

只有一行数据被扫描：const

![image-20210713190619853](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210713190619853.png)

多行数据被扫描：ref

![image-20210713190724932](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210713190724932.png)

**五、range**

range扫描就比较好理解了，它是索引上的范围查询，它会在索引上扫码特定范围内的值。

between，in，>都是典型的范围(range)查询。

![image-20210713191548841](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210713191548841.png)

**六、index**

index类型，需要扫描索引上的全部数据。

![image-20210713191703976](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210713191703976.png)

**七、ALL**

join中，对于前表的每一行(row)，后表都要被全表扫描。

单表全表扫描。



**全表扫描代价极大，性能很低，是应当极力避免的，通过explain分析SQL语句，非常有必要**。



**总结**

（1）explain结果中的**type字段**，表示（广义）连接类型，它描述了找到所需数据使用的扫描方式；

（2）常见的扫描类型有：

system>const>eq_ref>ref>range>index>ALL

其扫描速度由快到慢；

（3）各类扫描类型的要点是：

- **system**最快：不进行磁盘IO
- **const**：PK或者unique上的等值查询
- **eq_ref**：PK或者unique上的join查询，等值匹配，对于前表的每一行(row)，后表只有一行命中
- **ref**：非唯一索引，等值匹配，可能有多行命中
- **range**：索引上的范围扫描，例如：between/in/>
- **index**：索引上的全集扫描，例如：InnoDB的count
- **ALL**最慢：全表扫描(full table scan)

（4）建立正确的索引(index)，非常重要；

（5）使用explain了解并优化执行计划，非常重要；



# **Extra**

Extra 是 EXPLAIN 输出中另外一个很重要的列，该列显示 MySQL 在查询过程中的一些详细信息。

![img](https://pic3.zhimg.com/80/v2-83a0384e89892b1d16703c9cc1dd211a_720w.jpg)



## **Using index**

使用索引覆盖的情况下，执行计划的 extra 会显示为 "Using index"：

- 查询的字段都包含在使用的索引中；
- where 子句使用的字段也都包含在使用的索引中。

![image-20210720154820632](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210720154820632.png)



## **Using index condition**

查询数据时如果使用 index condition down 索引条件下推就会在执行计划的 extra 字段中出现 "Using index condition"。

使用二级索引查找数据时，**where 条件中属于索引一部分但无法使用索引的条件**（比如 like '%abc' 左侧字符不确定），

MySQL 也会把这部分判断条件下推到存储引擎层，**筛选之后再进行回表**，这样**回表时需要查找的数据就更少**。

![image-20210720155510761](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210720155510761.png)

第一个好理解，下图是为啥？

![image-20210720155607148](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210720155607148.png)

![image-20210720155652736](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210720155652736.png)



## **Using where**

就是前面说的 MySQL 服务层可以**把属于索引的一部分但又无法使用索引的条件下推到存储引擎层**，而其他条件还是得在 MySQL 服务层应用来过滤存储引擎层返回的数据。

当出现这的情况，执行计划的 extra 字段就会出现 "Using where"，它可以和 "Using index" 一起出现，也可以和 "Using index condition" 一起出现。

![image-20210720161046798](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210720161046798.png)

**在存储引擎检索行后再进行过滤**，使用了where从句来限制哪些行将与下一张表匹配或者是返回给用户。



**Extra:**

​	Distinct：在select部分使用了distinc关键字。MySQL发现第1个匹配行后，停止为当前的行组合搜索更多的行。
​	Using filesort：排序时无法使用到索引时，就会出现这个。

![image-20210726103428922](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20210726103428922.png)	Using temporary：表示使用了临时表存储中间结果。

​	Using index：索引覆盖。查询的列被索引覆盖，并且where筛选条件是**索引的前导列**。

​	**Using where ，Using index**：

​	查询的列被索引覆盖，并且where筛选条件是索引列之一，但不是索引的不是前导列，Extra中为Using where; Using index，
　　　 意味着无法直接通过索引查找来查询到符合条件的数据

​	**NULL（\**既没有Using index，也没有Using where Using index，也没有using where\**）**

　查询的列未被索引覆盖，并且where筛选条件是索引的前导列，
　　   意味着用到了索引，但是部分字段未被索引覆盖，必须通过“回表”来实现，不是纯粹地用到了索引，也不是完全没用到索引，Extra中为NULL(没有信息)

​	**Using where**

　　1，查询的列未被索引覆盖，where筛选条件非索引的前导列，Extra中为Using where；

​		2，查询的列未被索引覆盖，where筛选条件非索引列，Extra中为Using where

​		需要**回表**。**在查找使用索引的情况下，需要回表去查询所需的数据

​	**Using index condition**

​		1，查询的列不全在索引中，where条件中是一个前导列的范围

​		2，查询列不完全被索引覆盖，查询条件完全可以使用到索引（进行索引查找）

​	Using where：**需要回行。**在查找使用索引的情况下，需要回表去查询所需的数据
​	Using sort_union、Using union、Using intersect：这些函数说明如何为index_merge联接类型合并索引扫描。



# [MySQL 执行计划详解](https://www.cnblogs.com/yinjw/p/11864477.html)

https://www.cnblogs.com/yinjw/p/11864477.html

