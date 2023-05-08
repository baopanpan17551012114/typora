# 分区

```sql
create table if not EXISTS data_model.wsc_recommend_recall_with_more_action
 (
 pid  bigint,
 storeid bigint,
 goodsid  bigint,
 pv_number bigint,
 buynow_number bigint,
 share_number bigint,
 cart_number bigint,
 singledeletion_number bigint,
 ordered_number bigint,
 payment_number bigint,
 confirm_number bigint,
 score float
 )
 partitioned by (topicdate string)
 row format delimited
 stored as textfile;
```

插入指定分区：

```sql
INSERT OVERWRITE TABLE data_model.wsc_recommend_recall_with_more_action PARTITION (topicdate='2021-09-12')
select t1.pid, t2.storeid,
t1.goodsid, 
t1.pv_number,t1.buynow_number,
t1.share_number,t1.cart_number,
t1.singledeletion_number,
t1.ordered_number,t1.payment_number,t1.confirm_number, 0 as score
 from 
```



# Hive 桶

对于每一个表（table）或者分区， Hive可以进一步组织成桶，也就是说桶是更为细粒度的数据范围划分。Hive也是 针对某一列进行桶的组织。Hive采用对列值哈希，然后除以桶的个数求余的方式决定该条记录存放在哪个桶当中。

1.把表（或者分区）组织成桶（Bucket）有两个理由：

（1）获得更高的查询处理效率。桶为表加上了额外的结构，Hive 在处理有些查询时能利用这个结构。具体而言，连接两个在（包含连接列的）相同列上划分了桶的表，可以使用 Map 端连接 （Map-side join）高效的实现。比如JOIN操作。对于JOIN操作两个表有一个相同的列，如果对这两个表都进行了桶操作。那么将保存相同列值的桶进行JOIN操作就可以，可以大大较少JOIN的数据量。

（2）使取样（sampling）更高效。在处理大规模数据集时，在开发和修改查询的阶段，如果能在数据集的一小部分数据上试运行查询，会带来很多方便。



2.对桶中的数据进行采样：

hive> SELECT * FROMbucketed_users  
>   TABLESAMPLE(BUCKET 1 OUT OF 4 ON id);  
>   0 Nat  
>   4 Ann  

桶的个数从1开始计数。因此，前面的查询从4个桶的第一个中获取所有的用户。 对于一个大规模的、均匀分布的数据集，这会返回表中约四分之一的数据行。我们 也可以用其他比例对若干个桶进行取样(因为取样并不是一个精确的操作，因此这个 比例不一定要是桶数的整数倍)。

注：tablesample是抽样语句，语法：TABLESAMPLE(BUCKET x OUTOF y)
y必须是table总bucket数的倍数或者因子。hive根据y的大小，决定抽样的比例。例如，table总共分了64份，当y=32时，抽取(64/32=)2个bucket的数据，当y=128时，抽取(64/128=)1/2个bucket的数据。x表示从哪个bucket开始抽取。例如，table总bucket数为32，tablesample(bucket 3 out of 16)，表示总共抽取（32/16=）2个bucket的数据，分别为第3个bucket和第（3+16=）19个bucket的数据。
