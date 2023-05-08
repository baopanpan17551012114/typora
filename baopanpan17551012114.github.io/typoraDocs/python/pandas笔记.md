## 一、热点资讯计算（分组groupby、聚合agg、排序sort_values）

需求：在历史点击表中，按照category不同，统计热点资讯。

![img](https://pic1.zhimg.com/80/v2-f71e10ab1abb428f786c6d9ada4a6a0c_720w.jpg)历史点击表

思路：按照类别分组，然后按照informationid分组，统计数量，并按照数量时间降序

```python3
import numpy as np
import redis
from redis import StrictRedis
        
sql = """select * from alg_xk_xkb_for_information_recommendation_click order by topicdate desc"""
df = sqlBase.selectFromTable(sql)

# 按照 类别分组，然后按照informationid分组，统计数量，并按照数量时间降序
category_groups = df.groupby("category")
for category,groupDf in category_groups:
    groupDf = groupDf[["informationid", "topicdate"]]
    groupDf = groupDf.groupby("informationid").agg({"topicdate":max, "informationid":np.size}).rename(columns={"informationid":"count"})
    groupDf = groupDf.sort_values(["count", "topicdate"], ascending=False)
    print(groupDf)
```



![img](https://pic2.zhimg.com/80/v2-527d4532dc58197c7908cf0af5a649d1_720w.jpg)

备注：

1、groupby根据多列分组：

```text
groups = df.groupby(["category","informationid"])
for (categoryid, informationId), childDf in groups:
```

2、agg({"col1":max, "col2":np.size})中可以使用的方法，python内嵌方法和np中方法基本都可以；



## 二、合并DataFrame

merge：左右拼接（left join）

```python
df = df_user.merge(df_goods, on=["pid", "goodsid"])
```

concat：上下拼接

```python
df_goods = pd.concat([df_goods, df_new], ignore_index=True)
```

join

