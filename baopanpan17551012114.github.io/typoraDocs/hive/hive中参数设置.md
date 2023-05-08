# [hive参数设置](https://www.cnblogs.com/chenzechao/p/9365795.html)

 

```sql
-- 设置hive的计算引擎为spark
set hive.execution.engine=spark;

-- 修复分区
set hive.msck.path.validation=ignore;
msck repair table sub_ladm_app_click_day_cnt;

-- 打印表头
set hive.cli.print.header=true;
set hive.cli.print.row.to.vertical=true;
set hive.cli.print.row.to.vertical.num=1;

-- 显示当前数据库
set hive.cli.print.current.db=true;

// 开启任务并行执行
set hive.exec.parallel=true;
// 同一个sql允许并行任务的最大线程数
set hive.exec.parallel.thread.number=8;


-- 1、合并输入文件
-- 每个Map最大输入大小
set mapred.max.split.size=128000000;
-- 一个节点上split的至少的大小
set mapred.min.split.size.per.node=100000000;
-- 一个交换机下split的至少的大小
set mapred.min.split.size.per.rack=100000000;
-- 执行Map前进行小文件合并
set hive.input.format=org.apache.hadoop.hive.ql.io.CombineHiveInputFormat;

-- 2、合并输出文件
-- 在Map-only的任务结束时合并小文件
set hive.merge.mapfiles=true;
-- 在Map-Reduce的任务结束时合并小文件
set hive.merge.mapredfiles = true;
-- 合并文件的大小
set hive.merge.size.per.task = 134217728;
-- 当输出文件的平均大小小于该值时，启动一个独立的map-reduce任务进行文件merge
set hive.merge.smallfiles.avgsize=16000000;


-- pa
set hive.exec.parallel = true;
set hive.exec.parallel.thread.number=50;

set mapred.reduce.tasks=999;
set hive.merge.smallfiles.avgsize=100000000;
set mapred.combine.input.format.local.only=false;

-- 控制hive任务的reduce数
set hive.exec.reducers.bytes.per.reducer=200000000;
set hive.exec.reducers.max=150;
set hive.exec.compress.intermediate=true;

-- map执行前合并小文件，减少map数
set mapred.max.split.size=256000000;
set mapred.min.split.size=256000000;
set mapred.min.split.size.per.node=100000000;
set mapred.min.split.size.per.rack=100000000;
set hive.input.format=org.apache.hadoop.hive.ql.io.CombineHiveInputFormat;

set hive.merge.mapredfiles = true;
set hive.merge.smallfiles.avgsize=100000000;
set mapred.combine.input.format.local.only=false;

set hive.map.aggr=true;
set hive.groupby.skewindata=true;

set mapreduce.reduce.memory.mb=6144;
set mapreduce.reduce.java.opts=-Xms2000m -Xmx8192m;
set mapred.compress.map.output=true;
set Hive.optimize.skewjoin = true;
set Hive.skewjoin.key=10000000;
set hive.auto.convert.join=true;
set hive.mapjoin.smalltable.filesize=25000000;

set io.sort.spill.percent=0.6;
set mapred.job.shuffle.input.buffer.percent=0.2 ;
set mapred.job.shuffle.merge.percent=0.6;

set hive.orc.compute.splits.num.threads=50;

-- 修改reduce任务从map完成80%后开始执行
set mapreduce.job.reduce.slowstart.completedmaps=0.8

-- 加大内存
set mapreduce.map.memory.mb=16384;
set mapreduce.map.java.opts=-Xmx13106M;
set mapred.map.child.java.opts=-Xmx13106M;
set mapreduce.reduce.memory.mb=16384;
set mapreduce.reduce.java.opts=-Xmx13106M;--reduce.memory*0.8
set mapreduce.task.io.sort.mb=512


1 -- 从本地文件加载数据：
2 LOAD DATA LOCAL INPATH '/home/hadoop/input/ncdc/micro-tab/sample.txt' OVERWRITE INTO TABLE records;
3 load data local inpath '/home/hive/partitions/files' into table logs partition (dt='2017-08-01',country='GB');
 

1 -- 函数帮助
2 show functions;
3 desc function to_date;
4 desc function extended to_date;
 

1 -- 数组、map、结构
2 select col1[0],col2['b'],col3.c from complex;
 

1 -- 导出orc文件
2 hive --orcfiledump /user/hive/warehouse/sx_360_safe.db/user_reg_info_init2
 

1 -- 导出hive表数据
2 insert overwrite local directory '/tmp/tmp_20170830/app_210_s3_1016' row format delimited fields terminated by ',' select * from app_210_s3_1016;
3 cd /tmp/tmp_20170830/sub_ladm_exc_app_210_s3_1016
4 cat * > /tmp/tmp_20170830/result/app_210_s3_1016.csv
5 cd /tmp/tmp_20170830/result/
6 gzip app_210_s3_1016.csv
 

1 -- hive生成统一ID
2 select regexp_replace(reflect("java.util.UUID", "randomUUID"), "-", "");
 


-- 行转列功能
-- 打印列名
set hive.cli.print.header=true;
-- 开启行转列功能, 前提必须开启打印列名功能
set hive.cli.print.row.to.vertical=true;
-- 设置每行显示的列数
set hive.cli.print.row.to.vertical.num=1;
```

