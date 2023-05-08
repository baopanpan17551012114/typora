hive分区建立和数据插入

```sql
create table if not EXISTS algo_test.inshop_dynamic_distribute_gap_ab_result
(
  shop_id int,
  shop_name string,
  city_id int,
  test_id string,
  valid_bill_num float,
  finished_bill_num float,
  ontime_bill_num float,
  finished_ratio float,
  ontime_ratio float,
  zhudian_ratio float,
  zdontime_ratio float,
  zbontime_ratio float,
  10min_ratio float,
  fulltime_cnt float,
  parttime_cnt float,
  FT_PERF float,
  ZD_PERF float,
  ZB_cnt float,
  ZD_avg_dist float,
  ZD_avg_weight float,
  ZB_avg_dist float,
  ZB_avg_weight float,
  algo_assign_ratio float,
  human_assign_ratio float,
  human_gaipai_ratio float,
  zhuandan_ratio float,
  gaipai_plus_zhuandan_ratio float,
  avg_resident_full_dh float,
  avg_resident_full_wh float,
  avg_resident_dh float,
  avg_resident_wh float,
  assign_switch float,
  assign_limit_time float,
  inshop_feedback_num float,
  inshop_feedback_negative_num float
 )
 partitioned by (create_dt string)
 row format delimited
 stored as textfile;
 
 -- 写入分区表
INSERT overwrite TABLE algo_test.inshop_dynamic_distribute_gap_ab_result PARTITION (create_dt = '${data_dt}')
SELECT 
  max(shop_id) as shop_id,
  max(shop_name) as shop_name,
  max(city_id) as city_id,
  max(test_id) as test_id,
  avg(valid_bill_num) as valid_bill_num,
  avg(finished_bill_num) as finished_bill_num,
  avg(ontime_bill_num) as ontime_bill_num,
  avg(finished_ratio) as finished_ratio,
  avg(ontime_ratio) as ontime_ratio,
  avg(zhudian_ratio) as zhudian_ratio,
  avg(zdontime_ratio) as zdontime_ratio,
  avg(zbontime_ratio) as zbontime_ratio,
  avg(10min_ratio) as 10min_ratio,
  avg(fulltime_cnt) as fulltime_cnt,
  avg(parttime_cnt) as parttime_cnt,
  avg(FT_PERF) as FT_PERF,
  avg(ZD_PERF) as ZD_PERF,
  avg(ZB_cnt) as ZB_cnt,
  avg(ZD_avg_dist) as ZD_avg_dist,
  avg(ZD_avg_weight) as ZD_avg_weight,
  avg(ZB_avg_dist) as ZB_avg_dist,
  avg(ZB_avg_weight) as ZB_avg_weight,
  avg(algo_assign_ratio) as algo_assign_ratio,
  avg(human_assign_ratio) as human_assign_ratio,
  avg(human_gaipai_ratio) as human_gaipai_ratio,
  avg(zhuandan_ratio) as zhuandan_ratio,
  avg(gaipai_plus_zhuandan_ratio) as gaipai_plus_zhuandan_ratio,
  avg(avg_resident_full_dh) as avg_resident_full_dh,
  avg(avg_resident_full_wh) as avg_resident_full_wh,
  avg(avg_resident_dh) as avg_resident_dh,
  avg(avg_resident_wh) as avg_resident_wh,
  avg(assign_switch) as assign_switch,
  avg(assign_limit_time) as assign_limit_time,
  avg(inshop_feedback_num) as inshop_feedback_num,
  avg(inshop_feedback_negative_num) as inshop_feedback_negative_num
from
  algo_test.cs_alls_test_show01_01
 group by shop_id,
  shop_name,
  city_id,
  test_id 
 ;
```

