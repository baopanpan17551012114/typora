## Hive是什么？

**hive是一个基于Hadoop的一个数据仓库工具**。可以将结构化数据映射为一张数据库表，并且提供sql的查询能力，可以将sql换换为MapReduce任务进行。



**Hbase和Hive 二者都是以Hdfs为文件存储。**

Hbase支持列扩展，可以对单元格修改。采取K-V的设计，因此查询效率比较高，一般用于延迟忍耐低的场景；还有就是经常需要扩展属性，修改属性场景。

Hbase的查询一般通过命令窗口进行，语句比较负责，但是hive的采用标准的sql语法，门槛低，上手简单。当然Hbase也有Phoenix可以去支持 sql这样的语法操作。

**Hbase和Hive在大数据架构中处在不同位置，Hbase主要解决实时数据查询问题，Hive主要解决数据处理和计算问题，一般是配合使用。**

**一、区别：**

1. Hbase： Hadoop database 的简称，也就是基于Hadoop数据库，是一种NoSQL数据库，主要适用于海量明细数据（十亿、百亿）的随机实时查询，如日志明细、交易清单、轨迹行为等。
2. Hive：Hive是Hadoop数据仓库，严格来说，不是数据库，主要是让开发人员能够通过SQL来计算和处理HDFS上的结构化数据，适用于离线的批量数据计算。

- 通过元数据来描述Hdfs上的结构化文本数据，通俗点来说，就是定义一张表来描述HDFS上的结构化文本，包括各列数据名称，数据类型是什么等，方便我们处理数据，当前很多SQL ON Hadoop的计算引擎均用的是hive的元数据，如Spark SQL、Impala等；
- 基于第一点，通过SQL来处理和计算HDFS的数据，Hive会将SQL翻译为Mapreduce来处理数据；

**二、关系**

在大数据架构中，Hive和HBase是协作关系，数据流一般如下图：

1. 通过ETL工具将数据源抽取到HDFS存储；
2. 通过Hive清洗、处理和计算原始数据；
3. HIve清洗处理后的结果，如果是面向海量数据随机查询场景的可存入Hbase
4. 数据应用从HBase查询数据；

![img](https://pic1.zhimg.com/50/v2-2fbb6391206db40675afa8617806a8be_720w.jpg?source=1940ef5c)

ETL：是英文 Extract-Transform-Load 的缩写，用来描述将数据从来源端经过抽取（extract）、转换（transform）、加载（load）至目的端的过程。



### hbase具体的应用场景：

千万并发、PB存储、KV基础存储、动态列、强同步、稀疏表、二级索引、SQL

![img](https://upload-images.jianshu.io/upload_images/7063537-5691cc2300cd4da0?imageMogr2/auto-orient/strip|imageView2/2/w/640/format/webp)

### 下面再来看看Hive的具体使用场景：

1，分析网络日志。

2，ETL清洗数据。

3，构建数据仓库。

4，数据挖掘





在Hadoop基本生态中，有两个组件的说说他们的区别，它们就是hive和hbase。Hive是基于Hadoop的一个数据仓库工具，可以将结构化的数据文件映射为一张数据库表，并提供简单的sql查询功能，可以将sql语句转换为MapReduce任务进行运行。HBase是Hadoop的数据库，一个分布式、可扩展、大数据的存储。

1、Hive本身不存储和计算数据，它完全依赖于HDFS和MapReduce，**Hive中的表纯逻辑**。hive需要用到hdfs存储文件，需要用到MapReduce计算框架。

2、hive可认为是map-reduce的一个包装。hive的意义就是把好写的hive的sql转换为复杂难写的map-reduce程序。

3、hbase是物理表，不是逻辑表，提供一个超大的内存hash表，搜索引擎通过它来存储索引，方便查询操作。

4、hbase可以认为是hdfs的一个包装。他的本质是数据存储，是个NoSql（not only sql）数据库；hbase部署于hdfs之上，并且克服了hdfs在随机读写方面的缺点