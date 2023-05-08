

## 1.3 Hive与Hadoop的关系

Hive利用HDFS存储数据，利用MapReduce查询数据

 

 ![img](..\typora-user-images\1104796-20171201173550945-19779820.png)

 

 

## 1.4 Hive与传统数据库对比

 

 ![img](..\typora-user-images\1104796-20171201173600164-2099757210.png)

 

*总结：hive**具有sql**数据库的外表，但应用场景完全不同，hive**只适合用来做批量数据统计分析*

## 1.5 Hive的数据存储

1、Hive中所有的数据都存储在 HDFS 中，没有专门的数据存储格式（可支持Text，SequenceFile，ParquetFile，RCFILE等）

2、只需要在创建表的时候告诉 Hive 数据中的列分隔符和行分隔符，Hive 就可以解析数据。

3、Hive 中包含以下数据模型：DB、Table，External Table，Partition，Bucket。

² db：在hdfs中表现为${hive.metastore.warehouse.dir}目录下一个文件夹

² table：在hdfs中表现所属db目录下一个文件夹

² external table：与table类似，不过其数据存放位置可以在任意指定路径

² partition：在hdfs中表现为table目录下的子目录

² bucket：在hdfs中表现为同一个表目录下根据hash散列之后的多个文件..



## 1.4、Hive架构原理

![12、Hive核心概念与原理详解_Hive_02](..\typora-user-images\watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDQ1MzQwNA==,size_16,color_FFFFFF,t_70)
​ (1)用户接口：CLI（hive shell）；JDBC（java访问Hive）；WEBUI（浏览器访问Hive）
​ (2)元数据：MetaStore
元数据包括：表名、表所属的数据库（默认是default）、表的拥有者、列/分区字段，标的类型（表是否为外部表）、表的数据所在目录。这是数据默认存储在Hive自带的derby数据库中，推荐使用MySQL数据库存储MetaStore。
（3）Hadoop集群：
使用HDFS进行存储数据，使用MapReduce进行计算。
（4）Driver:驱动器：
解析器（SQL Parser）：将SQL字符串换成抽象语法树AST，对AST进行语法分析，像是表是否存在、字段是否存在、SQL语义是否有误。
编译器（Physical Plan）：将AST编译成逻辑执行计划。
优化器（Query Optimizer）：将逻辑计划进行优化。
执行器（Execution)：把执行计划转换成可以运行的物理计划。对于Hive来说默认就是Mapreduce任务。