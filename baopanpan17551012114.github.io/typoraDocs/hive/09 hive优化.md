# HIve 优化

谈起优化，上面很大一部分内容都有涉及，这里我们主要谈谈 HQL 优化

1. 某些容易导致数据倾斜的函数

- count(distinct *)
   该用法在MR 的 reduce 阶段只有一个 reduce 来处理，当数据量较大会导致严重的数据倾斜。
   可以通过
   `select count(1) from (select distinct(uid) d_uid from t)`
   来优化

- order by key
   对于全局排序也有上面类似的问题
   为了解决上面的问题，我们需要先了解下hive的几种排序

  - order by:全局排序，一般不用，因为其为了全局有序，
     将数据放到一个reduce里面处理，效率比较低
  - cluster by：全局排序，建议使用，但是只能是降序，不能指定asc和desc
  - sort by：局部排序，这个局部就是每个 reduce 内部，
     所以不能保证全局有序，
     单个使用意义不大，需要结合 `distribute by`一起使用
  - distribute by：分区排序，
     在分发数据给 reduce 的时候保证 reduce 是有序的，
     结合 `sort by`，
     可以做到全局有序

  所以上面这个问题可以通过
   `select a,b,c from t distribute by a sort by a asc, b desc`
   来解决

2.Union All insert



```sql
//union all
insert overwrite table t
select a,b,c   
from t1      
union all   
select a,b,c from t2

//普通方式
insert overwrite table t
select a,b,c  from t1
insert overwrite table t
select a,b,c  from t2
```

3.limit
 limit 限制，对于hive 很多时候都是需要进行全表统计，然后显示 limit 限制的条数的记录，对于执行速度并没有多大的提升，如果我们的需求是可以通过抽样来看整体情况的，那么全表扫描无疑是很浪费资源的，所以hive也是提供了相应的优化机制，来允许我们采用抽样使用 limit
 `hive.limit.optimize.enable=true --- 开启对数据源进行采样的功能`
 `hive.limit.row.max.size --- 设置最小的采样容量`
 `hive.limit.optimize.limit.file --- 设置最大的采样样本数`

4.join
 上文说了不少join的优化，这里补充一点：
 多个join 相互关联，尽可能的使用相同的 key 进行关联，
 这样就 Hive 就只会使用一个 Maper 去操作

5.本地执行
 当我们觉得有些任务是杀鸡用牛到的时候，
 可以尝试运用本地运行，
 分布式计算只有再数据量足够大的时候才能体现其优势，
 否则单机是更快的。
 通过 `mapred.job.tracker=true` 参数开启本地模式
 当然，hive 也提供了自动开启本地模式的优化机制
 通过 `hive.exec.mode.local.auto`开启自动执行，自动开启的条件如下：
 1.job的输入数据量小于参数：hive.exec.mode.local.auto.inputbytes.max(默认128MB)
 2.job的map数必须小于参数：hive.exec.mode.local.auto.tasks.max(默认4)
 3.job的reduce数必须为0或者1

6.stage 并行执行
 对于一些复杂的 hql 可能会需要多个 stage 一起执行来完成，默认情况下，hive 是根据其解析的计划串行执行一个个 stage 的，为了提高执行速度，我们可以开启 stage 并行执行。
 通过 `hive.exec.parallel=true`开启

> stage 是什么？ hive 将sql 解析后自动生成的一个抽象概念，比如我们要做饭，那么这个 stage 就可以理解为：1.买菜买米，2.煮饭，3.炒菜，4.上桌。如果开启了并行执行那么，煮饭 和 炒菜 就可以一起执行了，当然前提是你要忙的过来，所以集群资源要足够才行，如果没开启，那么就傻傻的 先煮饭，饭熟了，再炒菜

7.JVM 重用
 这里JVM 重用是针对 hive 底层的执行引擎 MR 来说的，默认的一个 task 启动一个 jvm 进程来执行。
 通过`mapred.job.reuse.jvm.num.tasks=1` 可以设置每个JVM 执行的 task 数量

> 为什么默认值是1 ？
>  因为如果JVM设置的task数量超过1，那么这个JVM只有等待其执行完自己指定的任务，或者整个 job执行完成才会退出释放资源。
>  我们可以来假设一下，我们有 6 个task分别是 1，2，3，4，5，6。对应执行的JVM 是 1到6 号，其中 1 需要1min 执行完，其余的都是1s执行完，如果一个jvm执行完一个任务就退出，那么可能在 2s 到 3s 的时候，就只有 1号JVM 占用资源了，而如果不是，2到6号 在 2-3s 执行完毕，但是因为有重用功能，会导致这几个 JVM 继续等待其他任务的派发，直到 1号JVM 执行完毕才会释放 ，导致资源的浪费。
>  所以才资源不是很充足的情况下，该功能还是要慎用噢~！

8.控制 Map 和 Reduce 数量
 [可以参考这篇文章](https://links.jianshu.com/go?to=https%3A%2F%2Fblog.csdn.net%2Fzhong_han_jun%2Farticle%2Fdetails%2F50814246)

https://blog.csdn.net/zhong_han_jun/article/details/50814246

> 这里啰嗦一下，reduce的数量可能需要根据你自己的业务来设置一下，
>  没什么可说，但是 Map 一般都不会需要自己设置和优化，
>  因为默认一个 Map就是对应 hdfs 上的一个 block，
>  当hdfs block设置合理，并且小文件很少的情况，
>  那么 Map 一般也还算合理并不会有多大影响。

