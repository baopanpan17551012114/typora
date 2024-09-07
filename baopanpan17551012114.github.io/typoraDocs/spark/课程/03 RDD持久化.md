RDD持久化

1、RDD的数据是过程数据

RDD之间进行相互迭代计算(Transformation的转换)，当执行开启后，新RDD的生成，代表老RDD的消失。RDD的数据是过程数据，只在处理过程中存在，一旦处理完成，就不见了。

这个特性可以最大化的利用资源，老旧RDD没用了，就从内存中清理，给后续的计算腾出内存空间。

消失后，第二次使用时，只能基于RDD血缘关系，从RDD1重新执行。

2、缓存cache

rdd.cache(): 无参数，内存中

rdd.persist(): 有参数，可以选择内存或者硬盘

缓存技术可以将过程RDD数据，持久化保存到内存或者硬盘上，但是这个保存在设定上是认为不安全的。比如断电或者硬盘损坏等。

设计上认为缓存是不安全的，缓存如果丢失了，就需要重新计算RDD，所以需要保存被缓存RDD的血缘关系。

RDD将自己分区的数据，每个分区自行将其数据保存在其所在的Executor内存和硬盘上。这是分散存储。

3、checkpoint

CheckPoint存储RDD数据，是集中收集各个分区数据进行存储，而不同于缓存的分散存储。

缓存和CheckPoint对比：

- CheckPoint不管分区数量多少，风险是一样的，缓存分区越多，风险越高；
- CheckPoint支持写入HDFS，缓存不行，HDFS是高可靠存储，CheckPoint被认为是安全的；
- CheckPoint不支持内存，缓存可以，缓存如果写内存，性能比CheckPoint更好一些；
- CheckPoint因为设计上认为是安全的，所以不保留血缘关系，而缓存因为设计上认为不安全，所以保留；

代码：

sc.setCheckpointDir(...)

rdd.checkpoint()

