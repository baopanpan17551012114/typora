# Spark SQL

SparkSQL是Spark的一个模块，用于处理海量结构化数据。

## 1、特点

- 融合性：SQL可以无缝集成在代码中，随时用SQL处理数据；
- 统一数据访问：一套标准API可以读写不同数据源(文件、数据库等)；
- Hive兼容：可以使用spark SQL直接计算并生成hive数据表；
- 标准化连接：支持标准化JDBC\ODBC连接，方便和各种数据库进行数据交互；

## 2、SparkSQL的数据抽象

- Pandas-dataframe：二维表数据结构，单机（本地）集合；
- SparkCore-RDD：**无标准数据结构**，存储什么数据都可以。分布式集合（分区）；
- SparkSQL-dataframe：**二维表数据结构**，分布式结合（分区）；

SparkSQL For JVM- dataset / SparkSQL For  Python/R-dataframe：SparkSQL其实有3类数据抽象对象：

SchemaRDD对象，已废弃

DataSet对象：可用于java，Scala语言

DataFrame对象：可用于Java、Scala、python、R

## 3、DataFrame概述

DataFrame是按照二维表格的形式存储数据，RDD则是存储对象本身

## 4、SparkSession对象

SparkContext：RDD阶段，程序的执行入口对象；

SparkSession：

- 用于SparkSQL编程作为入口对象
- 用于SparkCore编程，可以通过SparkSession对象中获取到SparkContext；

```python
# coding: utf-8
from pyspark.sql import SparkSession

if __name__ == '__main__':
    spark = SparkSession.builder.\
        appName('test').\
        master('local[*]').\
        getOrCreate()

    sc = spark.sparkContext

    df = spark.read.csv()
    df.createTempView('score')
    
    # SQL风格
    spark.sql("""
    select * from score where name='语文'
    """).show()
    
    # DSL风格
    df.where("name='语文'").show()
```

## 5、总结

- SparkSQL和hive同样，都是用于大规模SQL分布式计算的计算框架，均可以运行在YARN之上，在企业中广泛应用；
- SparkSQL的数据抽象为：SchemaRDD（废弃）、DataFrame（python、R、Java、Scala）、DataSet（Java、Scala）；
- DataFrame同样是分布式数据集，有分区可以并行计算，和RDD不同的是，DataFrame中存储的数据结构是以表格形式组织的，方便进行SQL计算
- DataFrame对比DataSet基本相同，不同的是DataSet支持泛型特性，可以让Java、Scala语言更好的利用到；
- SparkSession是2.0后推出的新执行环境入口对象，可以用于RDD、SQL等编程；