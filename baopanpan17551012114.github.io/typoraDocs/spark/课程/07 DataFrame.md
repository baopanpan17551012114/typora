# DataFrame

## 1、DataFrame的组成

DataFrame是一个二维表结构，那么表格结构就无法绕开的三个点：行、列、表结构描述。

比如，在mySql中一张表：由许多行组成、数据也被分成多个列、表也有表结构信息（列、列名、列类型、列约束等）。

基于这个前提，DataFrame的组成如下：

- 在结构层面：
  - StructType对象描述整个DataFrame的表结构
  - StructField对象描述一个列的信息
- 在数据层面
  - Row对象记录一行数据
  - Column对象记录一列数据并包含列的信息

更详细：https://zhuanlan.zhihu.com/p/613632966

## 2、DataFrame的代码构建

- 基于RDD

  - 调用spark
  - 通过StructType对象来定义DataFrame的RDD
  - 使用RDD的toDF方法转换RDD

  ```python
  # coding: utf-8
  from pyspark.sql import SparkSession
  from pyspark.sql.types import StructType, StringType, IntegerType
  
  if __name__ == '__main__':
      spark = SparkSession.builder.\
          appName('test').\
          master('local[*]').\
          getOrCreate()
  
      sc = spark.sparkContext
  
      # 方法1
      rdd = sc.textFile()
      df = spark.createDataFrame(rdd, schema=['name', 'age'])
  
      # 方法2
      schema = StructType().add('name', StringType(), nullable=True).\
          add('age', IntegerType, nullable=False)
      df = spark.createDataFrame(rdd, schema=schema)
  
      # 方法3
      df = rdd.toDF(['name', 'age'])
  ```

- 基于pandas的DataFrame：将pandas的DataFrame对象，转变为分布式的SparkSQL DataFrame对象。

```python
pdf = pd.DataFrame({})
df = spark.createDataFrame(pdf)
```

- 读取外部数据：sparksession.read.format('text|json|parquet|orc|avro|jdbc|......')

```python
schema = StructType().add('data', StringType(), nullable=True)
df = spark.read.format('text').schema(schema=schema).load('people.txt')
```

csv，json和parquet等不需要schema

## 3、DataFrame的入门操作

DataFrame支持两种风格进行编程，分别是：DSL风格、SQL风格

DSL语法风格：DSL称之为：领域特定语言。其实就是指DataFrame的特有API。DSL风格就是以调用API的方式来处理Data。

SQL语法风格：SQL风格就是使用SQL语句处理DataFrame的数据。

```python
# coding: utf-8
from pyspark.sql import SparkSession
from pyspark.sql.types import StructType, StringType, IntegerType

if __name__ == '__main__':
    spark = SparkSession.builder.\
        appName('test').\
        master('local[*]').\
        getOrCreate()

    df = spark.createDataFrame()

    # DSL风格
    df.select(['id', 'subject']).show()
    df.filter('score < 99').show()
    df.where('score < 99').show()
    df.groupBy('subject').count().show()
    # df.groupBy API的返回值GroupedData, 不是DataFrame
    # 它是一个有分组关系的数据结构, 有一些API供我们对分组做聚合
    # SQL: group by 后接上聚合: sum, avg, count, min, mean
    # GroupedData 类似于SQL分组后的数据结构, 同样有各种聚合方法
    # GroupedData 调用聚合方法后, 返回值依旧是DataFrame
    # GroupedData 只是一个中转的对象, 最终还是要获得DataFrame的结果

    # SQL风格
    # 注册DataFrame称为表, 然后通过使用spark.sql()来执行SQL语句查询, 结果返回一个DataFrame。
    # 将DataFrame注册成表的方式:
    df.createTempView('score')  # 注册一个临时视图(表)
    df.createOrReplaceTempView('score')  # 注册一个临时表, 如果存在进行替换
    df.createGlobalTempView('score')  # 注册一个全局表
    # 全局表: 跨SparkSession对象使用, 在一个程序内的多个SparkSession中均可调用, 查询前带上前缀: global_tmp
    spark.sql("select word, count(*) as cnt from words group by word order by cnt desc").show()
```

## 4、DataFrame数据写出

SparkSQL统一API写出DataFrame数据

统一API语法

```python
df.write.mode().format().option(K,V).save(PATH)
# mode，传入模式字符串可选：append 追加，overwrite 覆盖，ignore 忽略，error 重复就报异常（默认的）
# format，传入格式字符串，可选：text，csv，json，parquet，orc，avro，jdbc
# 注意text源只支持单列df写出
# option 设置属性，如：.option("sep",",")r
# save 写出的路径，支持本地文件和HDFS 
```

常见的源写出:

```python
# Write text 写出，只能写出一个单列数据
df.select(F.concat_ws("---","user_id","movie_id","rank","ts")).\
    write.\
    mode("overwrite").\
    format("text").\
    save("../data/output/sql/csv")
# Write CSV 写出
df.write.mode("overwrite").\
    format("CSV").\
    option("sep",",").\
    option("header",True).\
    save("../data/output/sql/csv")
# Write Json 写出
df.write.mode("overwrite").\
    format("json").\
    save("../data/output/sql/json")
# Write Parquet 写出
df.write.mode("overwrite").\
    format("parquet").\
    save("../data/output/sql/parquet")
# 不给format，默认以parquet写出
df.write.mode("overwrite").save("../data/output/sql/default")

# 将数据写出到Hive表中
df.write.mode("append|overwrite|ignore|error").saveAsTable(参数1，参数2)
# 参数1：表名，如果指定数据库，可以写：数据库.表名 参数2：格式，推荐使用parquet
df.write.mode("overwrite").insertInto("algo_test.bpp_jdmc_wave_order_info_new")
# 写出到JDBC会自动创建表, 因为DataFrame中有表结构信息, StructType记录的 各个字段的 名称 类型 和是否允许为空
```

5、总结

- DataFrame在结构层面由StructField组成列描述，由StructType构造表描述。在数据层面，Column对象记录列数据，Row对象记录行数据。
- DataFrame可以从RDD转换、Pandas DF转换、读取文件、读取JDBC等方法构建。
- spark.read.format()和df.write.format()是DataFrame读取和写出的统一化标准API。
- SparkSQL默认在Shuffle阶段200个分区，可以修改参数获得最好性能。
- dropDuplicates可以去重，dropna可以删除缺失值，fillna可以填充缺失值。
- SparkSQL支持JDBC读写，可用标准API对数据库进行读写操作。
