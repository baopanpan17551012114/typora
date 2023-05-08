# hive事务

https://www.cnblogs.com/chenzechao/p/9318379.html

```sql
set hive.support.concurrency=true;
set hive.exec.dynamic.partition.mode=nonstrict;
set hive.txn.manager=org.apache.hadoop.hive.ql.lockmgr.DbTxnManager;
set hive.compactor.initiator.on=true;
set hive.compactor.worker.threads=1;

drop table tmp_ccc_20180716;
create table tmp_ccc_20180716(id int,age int)
clustered by (id) into 8 buckets
stored as orc 
TBLPROPERTIES ('transactional'='true')
;
-- 建表语句必须带有 into buckets 子句和 stored as orc TBLPROPERTIES ('transactional'='true') 子句，并且不能带有 sorted by 子句。
insert into table tmp_ccc_20180716 values(1,2);
insert into table tmp_ccc_20180716 values(2,3);
insert into table tmp_ccc_20180716 values(3,4);
update tmp_ccc_20180716 set age = 2 where id = 1;
delete from tmp_ccc_20180716 where id = 2;
select * from tmp_ccc_20180716;

drop table tmp_ccc_20180716;
create table tmp_ccc_20180716(id int,age int)
clustered by (id) into 8 buckets
stored as orc TBLPROPERTIES ('transactional'='true')
;
```



开始事务参数：

```sql
set hive.support.concurrency=true;
set hive.txn.manager=org.apache.hadoop.hive.ql.lockmgr.DbTxnManager;
```

建表：

```sql
create table data_model.test1(
id int,
pid bigint,
storeid bigint,
goodsid bigint,
label int
)
clustered by (id) into 8 buckets
stored as orc 
TBLPROPERTIES ('transactional'='true');
```

增删改查：

```sql
INSERT into TABLE data_model.test1 
select 1 as id, pid, store_id, goods_id, 0 as label 
from default.saas_xd_b2c_store_goods_relationship limit 10;

select * from data_model.test1;

update data_model.test1 set label = 2 where pid = 100000841712;

delete from data_model.test1 where label = 2;
```



Apache Hive 0.13 版本引入了事务特性，能够在 Hive 表上实现 ACID 语义，包括 INSERT/UPDATE/DELETE/MERGE 语句、增量数据抽取等。

Hive 3.0 又对该特性进行了优化，包括改进了底层的文件组织方式，减少了对表结构的限制，以及支持条件下推和向量化查询。
