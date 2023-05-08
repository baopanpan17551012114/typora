row_number() over(partition by sex order by age desc)

```sql
这句sql 就是按照下列方式进行分组—>排序–>打标签–>形成新的字段
0)原始表格数据

1,18,a,male
2,19,b,male
3,22,c,female
4,16,d,female
5,30,e,male
6,26,f,female

1)先按照sex 字段对t_person表进行分组,结果如下

1,18,a,male
2,19,b,male
5,30,e,male
---------------按照sex 字段分组,分为male /female
4,16,d,female
3,22,c,female
6,26,f,female

2)对每组数据按照age字段进行排序

5,30,e,male
2,19,b,male
1,18,a,male
---------------再对每组内按照age 字段排序
6,26,f,female
3,22,c,female
4,16,d,female

3)对每组数据从头到尾进行打标签

5,30,e,male   1
2,19,b,male    2
1,18,a,male    3
---------------再对每组内数据打标签,形成新的字段
6,26,f,female   1
3,22,c,female   2
4,16,d,female   3
```

