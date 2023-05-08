表中字段：wid, merchant_id, goodsid, recommendType, label, topicdate

![img](https://pic2.zhimg.com/80/v2-21285c405ca50a648ff14c146d48eef1_720w.jpg)

## 一、关系运算

1.等值比较: =

2.不等值比较: <>

3.小于比较: <

4.小于等于比较: <=

5.大于比较: >

6.大于等于比较: >=

7.空值判断: IS NULL

8.非空判断: IS NOTNULL

举例：统计表中字段label非空数量：

select count(*) from wsc_recommend_pos_neg_for_ctr_new where label is not null;

9.LIKE比较: LIKE (注意：否定比较时候用NOT ALIKE B)

举例：统计表中字段label为'expo'开头的数量：

select count(*) from wsc_recommend_pos_neg_for_ctr_new where label like 'expo%';

10. JAVA的LIKE操作: RLIKE

A RLIKE B，如果字符串A符合JAVA正则表达式B的正则语法，则为TRUE；否则为FALSE。

## 二、数学运算

1. 加法操作: +

2. 减法操作: -

3. 乘法操作: *

4. 除法操作: /

5. 取余操作: %

6. 位与操作: &

7. 位或操作: |

8. 位异或操作: ^

9．位取反操作: ~

## 三、逻辑运算

1. 逻辑与操作: AND

2. 逻辑或操作: OR

3. 逻辑非操作: NOT

## 四、数值计算

1. 取整函数: round （遵循四舍五入）

2. 指定精度取整函数: round(double a, int d)

3. 向下取整函数: floor

4. 向上取整函数: ceil

6. 取随机数函数: rand 返回一个0到1范围内的随机数

7. 自然指数函数: exp

8. 以10为底对数函数: log10(double a)

9. 以2为底对数函数: log2(double a)

10. 对数函数: log(double base, double a)

11. 幂运算函数: pow(double a, double p)

12. 开平方函数: sqrt(double a)

13. 二进制函数: bin(BIGINT a)

14. 十六进制函数: hex(BIGINT a)

15. 反转十六进制函数: unhex(string a)

16. 进制转换函数: conv(BIGINT num, int from_base, int to_base)

17. 绝对值函数: abs

18. 正取余函数: pmod

19. 正弦函数: sin

20. 反正弦函数: asin

21. 余弦函数: cos

22. 反余弦函数: acos

23. positive函数: positive

24. negative函数: negative

## 五、日期函数

1. UNIX时间戳转日期函数:from_unixtime from_unixtime(1323308943,'yyyyMMdd')

2. 获取当前UNIX时间戳函数:unix_timestamp

3. 日期转UNIX时间戳函数:unix_timestamp unix_timestamp('2011-12-07 13:01:03')

4. 日期时间转日期函数:to_date 返回日期时间字段中的日期部分

5. 日期转年函数: year 返回日期中的年

6. 日期转月函数: month 返回日期中的月份

7. 日期转天函数: day 返回日期中的天

8. 日期转小时函数: hour

9. 日期转分钟函数: minute

10. 日期转秒函数: second

11. 日期转周函数:weekofyear

12. 日期比较函数: datediff datediff('2012-12-08','2012-05-09')

13. 日期增加函数: date_add date_add('2012-12-08',10)

14. 日期减少函数: date_sub date_sub('2012-12-08',10)

## 六、条件函数

1. If函数: if

select if(label='click',get_json_object(goodsid, '$.[0]'), goodsid) as goodsid
from data_model.wsc_recommend_pos_neg_for_ctr_new limit 10;

2. 非空查找函数: COALESCE(T v1, T v2,…)

返回参数中的第一个非空值；如果所有值都为NULL，那么返回NULL

3. 条件判断函数：CASE

CASE a WHEN b THEN c [WHEN d THEN e]* [ELSE f] END

如果a等于b，那么返回c；如果a等于d，那么返回e；否则返回f

select case label
when 'click' then get_json_object(goodsid, '$.[0]')
when 'expose' then goodsid
else 'null' end
as goodsid from data_model.wsc_recommend_pos_neg_for_ctr_new limit 10;

CASE WHEN a THEN b [WHEN c THEN d]* [ELSE e] END

如果a等于b，那么返回c；如果a等于d，那么返回e；否则返回f

select case
when label='click' then get_json_object(goodsid, '$.[0]')
when label='expose' then goodsid
else 'null' end
as goodsid from data_model.wsc_recommend_pos_neg_for_ctr_new limit 10;

## 七、字符串函数

1. 字符串长度函数：length(string A)

2. 字符串反转函数：reverse(string A)

3. 字符串连接函数：concat(string A, string B…)

4. 带分隔符字符串连接函数：concat_ws(string SEP, string A, string B…)

5. 字符串截取函数：substr(string A, int start, int len),substring(string A, intstart, int len)

6. 字符串转大写函数：upper(string A) ucase(string A)

7. 字符串转小写函数：lower(string A) lcase(string A)

8. 去空格函数：trim 去除字符串两边的空格

9. 左边去空格函数：ltrim

10. 右边去空格函数：rtrim

11. 正则表达式替换函数：regexp_replace

将字符串A中的符合java正则表达式B的部分替换为C regexp_replace('foobar', 'oo|ar', '')

12. 正则表达式解析函数：regexp_extract(string subject, string pattern, int index)

将字符串subject按照pattern正则表达式的规则拆分，返回index指定的字符。

regexp_extract('foothebar', 'foo(.*?)(bar)', 1)

13. URL解析函数：parse_url(string urlString, string partToExtract [, stringkeyToExtract])

返回URL中指定的部分。partToExtract的有效值为：HOST, PATH, QUERY, REF, PROTOCOL, AUTHORITY, FILE, and USERINFO.

14. json解析函数：get_json_object()

get_json_object(recgslt,'$.[0:].id')as goodsid

15. 空格字符串函数：space(int n)
16. 重复字符串函数：repeat(string str, int n)
17. 首字符ascii函数：ascii(string str)
18. 左补足函数：lpad(string str, int len, string pad) 将str进行用pad进行左补足到len位
19. 右补足函数：rpad(string str, int len, string pad)
20. 分割字符串函数: split(string str, stringpat) 返回array
21. 集合查找函数:find_in_set(string str, string strList) find_in_set('ab','ef,ab,de')
22. 字符串查找函数：locate(subString, string)，没找到返回0，locate('confidence', coord_split)

instr(string str, string substr)功能类似

## 八、集合统计函数

1. 个数统计函数: count(*), count(expr), count(DISTINCT expr[, expr_.])

总和统计函数: sum(col), sum(DISTINCT col)

3. 平均值统计函数: avg(col), avg(DISTINCT col)

4. 最小值统计函数: min(col)

5. 最大值统计函数: max(col)

6. 非空集合总体变量函数:var_pop(col) 方差

7. 非空集合样本变量函数:var_samp(col)

(SUM(expr*expr)-SUM(expr)*SUM(expr)/COUNT(expr))/(COUNT(expr)-1)

8. 总体标准偏离函数:stddev_pop(col) 标准差

9. 样本标准偏离函数:stddev_samp(col) var_samp(col)开方

10．中位数函数:percentile(BIGINT col, p)

percentile(BIGINT col, array(p1 [, p2]…))

11. 近似中位数函数:percentile_approx(DOUBLE col, p [, B])

percentile_approx(DOUBLE col, array(p1 [, p2]…) [, B])

12. 直方图:histogram_numeric(col, b)

## 九、复合类型构建操作

1. Map类型构建: map (key1, value1, key2, value2,…)

2. Struct类型构建: struct(val1, val2, val3,…)

3. array类型构建: array(val1, val2,…)

select m['0'] from

(select map(recommendType, goodsid)as m from

data_model.wsc_recommend_pos_neg_for_ctr_new limit 10) as t;

## 十、复杂类型访问操作

1. array类型访问: A[n]

2. map类型访问: M[key]

3. struct类型访问: S.x 返回结构体S中的x字段

## 十一、复杂类型长度统计函数

1.

类型长度函数: size(Map<K.V>)

2.array类型长度函数: size(Array<T>)

举例：统计goodsid中元素个数

select size(split(goodsid, ',')) from wsc_recommend_pos_neg_for_ctr_new limit 10;

3.类型转换函数

select cast(recommendType as bigint) from wsc_recommend_pos_neg_for_ctr_new limit 10;



Array:

sort_array()

array_contains()