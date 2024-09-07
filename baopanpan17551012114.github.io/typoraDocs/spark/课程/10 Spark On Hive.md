## Spark On Hive

1、原理

对于Hive来说,就2东西:

- SQL优化翻译器(执行引擎),翻译SQL到MapReduce并提交到YARN执行；

- MetaStore元数据管理中心；

Spark On Hive

对于Spark来说，自身是一个执行引擎，但是 Spark自己没有元数据管理功能，当我们执行:

SELECT * FROM person WHERE age > 10

的时候，Spark完全有能力将SQL变成RDD提交。

但是问题是，Person的数据在哪？Person有哪些字段？字段啥类型？Spark完全不知道了。不知道这些东西，如何翻译RDD运行。

在SparkSQL代码中可以写SQL，那是因为，表是来自DataFrame注册的，DataFrame中有数据，有字段，有类型，足够Spark用来翻译RDD用。如果以不写代码的角度来看，SELECT * FROM person WHERE age > 10 spark无法翻译，因为没有元数据。

解决方案

Spark提供执行引擎能力，Hive的MetaStore 提供元数据管理功能。让Spark和Metastore连接起来,那么:

Spark On Hive 就有了:

引擎: spark

元数据管理: metastore

总结:
Spark On Hive 就是把Hive的MetaStore服务拿过来给Spark做元数据管理用而已。市面上元数据管理的框架很多，为什么非要用Hive内置的MetaStore？

### 2 配置

根据原理,就是Spark能够连接上Hive的MetaStore就可以了。

所以:

1. MetaStore需要存在并开机
2. Spark知道MetaStore在哪里( IP端口号)

### 总结

Spark On Hive 就是因为Spark自身没有元数据管理功能， 所以使用Hive的Metastore服务作为元数据管理服务。计算由Spark执行。