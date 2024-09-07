# RDD

## 1、为什么需要RDD

分布式计算需要：分区控制、shuffle控制、数据存储/序列化/发送、数据计算API等。

这些功能不能简单的通过python内置的本地集合(如list/字典等)去完成。在分布式框架中，需要一个统一的数据抽象对象来实现上述分布式计算所需功能，这个抽象对象就是RDD。

## 2、什么是RDD

RDD(Resilient Distributed Dataset)弹性可分布式数据集，是spark中最基本的数据抽象，代表一个**不可变、可分区**、里面的元素**可并行计算的**集合。

Dataset：一个数据集合，用于存放数据。（list/dict等是本地集合，存放在一个进程内部，RDD数据是跨机器存储的，跨进程）

Distributed：RDD中的数据是分布式存储的，可用于分布式计算。

Resilient：RDD中的数据可以存储在内存中或者磁盘中。

**弹性：**

- **存储的弹性：**内存与磁盘可以自动切换；RDD 的数据默认存放在内存中，当内存资源不足时，spark 会自动将 RDD 数据写入磁盘。
- **容错的弹性：**数据丢失可以自动恢复；RDD 可以通过自己的数据来源重新计算该 partition
- **计算的弹性：**计算出错的话，可以进行重试机制；
- **分片的弹性：**可根据需要重新进行分片

## 3、RDD的五大特征

- RDD是有分区的
- 计算方法会作用到每一个分区上
- RDD之间是有相互依赖关系的
- KV型RDD可以有分区器
- RDD分区的读取会尽量靠近数据所在地

## 4、SparkContext对象

Spark RDD编程的程序入口对象是SparkContext对象(不论何种编程语言)，只有构建出SparkContext，基于它才能执行后续的API调用和计算。本质上，SparkContext对编程来说，主要功能就是创建第一个RDD出来

## 5、RDD的创建

- 并行化创建，将本地集合->分布式RDD，sparkcontext.parallelize(参数1, 参数2)--参数1: 集合对象即可，如list；参数2：分区数。获取分区数的API为：getNumPatitions()

- 读取文件创建，可以读取本地数据，也可以读取HDFS数据。sparkcontext.textFile(参数1, 参数2)--参数1:文件路径；参数2：最小分区数量，一般可以不设置。

## 6、RDD算子

算子：分布式集合对象上的API称为算子(本地对象叫做方法/函数)。

算子分类：

Transformation：转换算子，返回值为RDD。这类算子是懒加载的，如果没有action算子，transformation算子是不工作的。

Action：动作算子，返回值为本地集合。

transformation算子相当于在构建执行计划，action算子是一个指令让这个执行计划开始工作的。

## 7、常用算子

Transformation算子：

| 算子                                                   | 参数                                                         | 功能                                                         |
| ------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| map(func)                                              |                                                              | 将RDD的数据一条条处理（处理逻辑基于map算子中接收的处理函数） |
| flatMap(func)                                          |                                                              | 对RDD执行map操作，然后进行解除嵌套操作（n层嵌套会变成一维list） |
| reduceByKey(func)                                      | func(A, A)->A                                                | **针对KV型RDD**，自动按照key分组，然后根据提供的聚合逻辑，完成组内数据(value)的聚合操作。聚合逻辑：累加式聚合，eg (((1+2)+3)+4) |
| mapValues(func)                                        | func(V)->U，传入的参数V是二元元组的value值                   | **针对二元元组RDD**，对其内部的二元元组的value执行map操作。map算子可以实现。 |
| groupBy(func)                                          | func(T)->K                                                   | 将RDD的数据进行分组，func函数返回值相同的为一个组。分组完成后，每一个组是一个**二元元组**，key是返回值，所有同组的输入数据放入一个迭代器对象中作为value。 |
| filter(func)                                           | func(T)->bool                                                | 过滤想要的数据                                               |
| distinct(参数1)                                        | 参数1，去重分区数量，一般不传                                | 对RDD数据进行去重                                            |
| rdd1.union(rdd2)                                       |                                                              | 2个RDD合并成一个RDD。不同类型的RDD可以混合。                 |
| rdd1.join(rdd2)                                        | Join--内连接；leftOuterJoin--左外；rightOuterJoin--又外      | 对两个RDD执行join操作(类似SQL)。**用于二元元组(KV型)**，按照K进行拼接 |
| rdd1.intersection(rdd2)                                |                                                              | 两个RDD交集                                                  |
| glom()                                                 |                                                              | 将RDD数据加上嵌套，这个嵌套按照分区来进行                    |
| groupByKey()                                           | KV型数据                                                     | 针对KV型RDD，自动按照key分组                                 |
| sortBy(func, ascending=False, numPartitons=1)          | func(T)->U定义排序；numPartitons用多少分区排序(分区多不一定全局有序) | 对RDD数据进行排序。如果要全局有序，分区数设置为1。           |
| sortByKey(ascending=False, numPartitons=None, keyfunc) | keyfunc(K)->U：可选，在排序前对key进行处理，不影响数据本身   | 针对KV型RDD，按照key进行排序                                 |

groupByKey()和reduceByKey()的区别：

功能上：groupByKey仅有分组功能，reduceByKey除了分组功能，还有reduce聚合功能(group+reduce)

性能上：reduceByKey的性能远大于groupByKey+聚合逻辑。groupByKey只能分组，所以，执行上是先分组(shuffle)后聚合。而reduceByKey由于自带聚合逻辑，所以可以现在分区内预聚合，然后再走分组(shuffle)，分组后再做最终聚合。**区别在于，预聚合后，在shuffle分组节点，被shuffle的数据可以极大的减少，进而极大的提升性能**。

```python
# coding: utf-8
from pyspark import SparkConf, SparkContext

conf = SparkConf().setMaster("local[*]").setAppName("WordCountHelloWorld")
sc = SparkContext(conf=conf)

"""单维"""
rdd1 = sc.parallelize([1, 2, 3, 4, 5, 6, 6, 3, 4])
# groupBy
rdd2 = rdd1.groupBy(lambda x: 1 if (x % 2 == 0) else 2)
# print(rdd2.map(lambda x: (x[0], list(x[1]))).collect())
# filter
rdd3 = rdd1.filter(lambda x: x % 2 == 0)
# distinct
rdd4 = rdd1.distinct()
# union
rdd5 = rdd1.union(sc.parallelize(['a', ['b', 1]]))
# join
rdd6 = sc.parallelize([(1001, "zhangsan"), (1002, "lisi")]).join(sc.parallelize([(1001, "tech"), (1002, "ui")]))
# intersection
rdd7 = rdd1.intersection(sc.parallelize([1, 2]))
print(rdd7.glom().collect())

"""多维"""
rdd1 = sc.parallelize([[1, 2, 3], [4, 5, 6]])
# map
rdd2 = rdd1.map(lambda x: x + [2])
# flatMap
rdd3 = rdd1.flatMap(lambda x: x + [2])

"""KV型"""
rdd1 = sc.parallelize([('a', 1), ('a', 2), ('b', 3), ('c', 4), ('c', 5)])
# reduceByKey
rdd4 = rdd1.reduceByKey(lambda a, b: a**b)
# mapValues
rdd5 = rdd1.mapValues(lambda x: x * 10)
# groupByKey
rdd6 = rdd1.groupByKey()
# sortBy
rdd7 = rdd1.sortBy(lambda x: x[1])
# sortByKey
rdd8 = rdd1.sortByKey(keyfunc=lambda x: str(x).lower())
print(rdd8.collect())
```

Action算子

| 算子                            | 参数                                                         | 返回值                     | 功能                                                         |
| ------------------------------- | ------------------------------------------------------------ | -------------------------- | ------------------------------------------------------------ |
| countByKey()                    |                                                              | dict                       | 统计key出现的次数（一般针对KV型RDD）                         |
| collect()                       |                                                              |                            | 将RDD各个分区的数据拉取到Driver，形成一个list对象。如果分布式对象RDD太大，会把Driver内存撑爆 |
| reduce(func)                    | func(T, T)->T，                                              | 返回值类型和参数类型要一致 | 对RDD数据按照传入的逻辑进行聚合。                            |
| fold(n, func)                   | 初始值聚合，会作用在**分区内聚合**和**分区间聚合**           |                            | 接受传入的逻辑进行聚合，聚合带有初始值                       |
| first()                         |                                                              |                            | 返回RDD的第一个元素                                          |
| take(N)                         | int，大于元素总个数不会报错                                  | list                       | 返回RDD的前N个元素                                           |
| count()                         |                                                              | N，数字                    | 计算RDD有多少条数据                                          |
| takeSample(参数1, 参数2, 参数3) | 参数1：bool类型，true表示可放回，false表示不可放回；参数2：抽样数量；参数3--可选：随机数种子 | list                       |                                                              |
| takeOrdered(参数1, 参数2)       | 参数1：数据量；参数2：对排序的数据进行更改(不会更改数据本身，可以控制升序和降序) |                            | 对RDD进行排序取前N个                                         |
| foreach(func)                   | func(T)->None                                                |                            | 对RDD中每一个元素，执行func提供的逻辑操作(同map)，但是这个方法没有返回值。**在executor直接执行** |
| saveAsTextFile()                | 文件路径中的文件夹不能已存在                                 |                            | 将RDD的数据写入文本文件中，支持本地写出和HDFS等文件系统。**在executor直接执行**，分布式写出 |

```python
# coding: utf-8
from pyspark import SparkConf, SparkContext

conf = SparkConf().setMaster("local[*]").setAppName("WordCountHelloWorld")
sc = SparkContext(conf=conf)
"""KV型"""
rdd1 = sc.parallelize([('a', 1), ('a', 2), ('b', 3), ('c', 4), ('c', 5)])
# ountByKey
value1 = rdd1.countByKey()
# reduce
value2 = rdd1.reduce(lambda a, b: (a[0] + b[0], a[1] + b[1]))
# fold
value3 = sc.parallelize(range(10), 3).fold(10, lambda a, b: a + b)
# first
value4 = rdd1.first()
# take
value5 = rdd1.take(8)
# count
value6 = rdd1.count()
# takeSample
value7 = rdd1.takeSample(False, 3)
# takeOrdered
value8 = rdd1.takeOrdered(3, lambda x: x[1])
# foreach
rdd1.foreach(lambda x: print(x))

rdd1.saveAsTextFile('data/data')
```

分区操作算子

| 算子                               | 类型           | 功能                                                         |
| ---------------------------------- | -------------- | ------------------------------------------------------------ |
| mapPartitions                      | Transformation | 在分区上执行map                                              |
| foreachPartition                   | action         | 和普通foreach一致，一次处理的是一整个分区数据                |
| partitionBy(N, func), func(K)->int | Transformation | 对RDD进行自定义分区操作。**针对二元元组，func(K)中对应K为key**。 |
| repartition(N)                     | Transformation | 对RDD重新分区(仅数量)                                        |

```python
# coding: utf-8
from pyspark import SparkConf, SparkContext

conf = SparkConf().setMaster("local[*]").setAppName("WordCountHelloWorld")
sc = SparkContext(conf=conf)

rdd1 = sc.parallelize(list(range(10)), 3)
# mapPartitions
rdd2 = rdd1.mapPartitions(lambda x: [ele * 10 for ele in x])
print(rdd2.glom().collect())

# foreachPartition
rdd1.foreachPartition(lambda data: print('-'*10 + '\n', [ele for ele in data]))

rdd1_1 = sc.parallelize([(a, b) for (a, b) in zip(range(10), range(10))])
# partitionBy
rdd3 = rdd1_1.partitionBy(5, lambda x: x % 5)
# repartition
rdd4 = rdd1.repartition(1)
print(rdd4.glom().collect())
```

## 8、总结

1. RDD创建的几种方法
   1. 通过并行化集合的方式(本地结婚转分布式集合)
   2. 读取数据的方式
2. Transformation和Action的区别
   1. 转换算子返回值是RDD，操作算子返回值不是RDD
   2. 转换算子是懒加载的，只有遇到操作算子才会执行
3. 哪两个算子不经过Driver直接输出
   1. foreach和saveAsTextFile，直接有executor执行后输出，不会将结果发送到Driver上
4. groupByKey()和reduceByKey()的区别
   1. reduceByKey自带聚合逻辑，groupByKey不带
   2. 如果做数据聚合reduceByKey效率更高，因为可以先聚合后shuffle再最终聚合，传输的IO小
5. mapPartitions和foreachPartition的区别
   1. mapPartitions带有返回值，foreachPartition不带
6. 对于分区操作有什么要注意的地方
   1. 尽量不要增加分区，可能破坏内存迭代的计算管道