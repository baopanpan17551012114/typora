# pyspark系列--pandas与pyspark对比

https://zhuanlan.zhihu.com/p/34901585



```python
# coding:utf-8
from pyspark import SparkConf
from pyspark.sql import SparkSession, SQLContext
import pyspark.sql.functions as F
from pyspark.sql.functions import lit
from pyspark.sql import Row  # 继承自tuple
from pyspark.sql.functions import col


conf = SparkConf().setAppName("test").setMaster("local")  # 本地
spark = SparkSession.builder.config(conf=conf).getOrCreate()
sc = spark.sparkContext

def get_from_db():
        prop = {'user': 'xxx',
        'password': 'xxx',
        'driver': 'com.mysql.cj.jdbc.Driver'}
        url = 'jdbc:mysql://host:3306/database'
        df = spark.read.jdbc(url=url, table='alg_ec_recommend_sale_for_recall_storeid', properties=prop)

        # df.cache()
        # SQLContext.clearCache() # 保存和清除缓存

        # 1、选择和切片筛选
        # 选取指定列
        df_child = df.select("storeid", "goodsid", "sale_number")
        # 切片+选择
        df_child = df.filter(df["sale_number"]>=50).select("storeid", "goodsid", "sale_number")
        df_child = df.filter(df.sale_number.between(50, 100)).select("storeid", "goodsid", "sale_number")

        # 增减列、重命名、列合并、dataFrame合并
        # 删除一列
        df_child_new = df_child.drop("storeid")
        # 新增一列
        df_child_new = df_child_new.withColumn("new_col", lit(0))
        # 重命名
        df_child_new = df_child_new.withColumnRenamed("new_col", "zero")
        # 列合并
        df_child_new = df_child_new.withColumn("concat", F.concat_ws("-", "zero", "sale_number"))
        # dataFrame左右拼接
        df_merge = df_child_new.join(df_child, on=["goodsid"], how="left")
        # dataFrame上下拼接
        df_union = df_child_new.select("goodsid", "sale_number").union(df_child.select("goodsid", "sale_number"))

        # 分组、统计
        df_group = df_child.groupby("storeid").agg({"sale_number":"max"}).withColumnRenamed("max(sale_number)","max_sale_number")
        df_group = df_child.groupby("storeid").agg(F.max(df_child["sale_number"])).withColumnRenamed("max(sale_number)","max_sale_number")

        # 排序
        df_sort = df_child.sort(df_child.storeid.desc(), df_child.sale_number.asc())

        # 和pandas相互转化
        df_pd = df_sort.toPandas()
        df_spark = SQLContext(sc).createDataFrame(data=df_pd)
        df_spark.show()

if __name__ == '__main__':
    df = get_from_db()
```



|     function     |                           pyspark                            |                  pandas                   |
| :--------------: | :----------------------------------------------------------: | :---------------------------------------: |
|  选取指定(多)列  |               df.select("storeid", "goodsid")                |        df[["storeid", "goodsid"]]         |
|       切片       | df.filter(df["sale_number"]>=50) <br>df.filter(df.sale_number.between(50, 100)) |         df[df["sale_number"]>=50]         |
|     删除一列     |                      df.drop("storeid")                      |           del(df_pd["storeid"])           |
|     新增一列     |               df.withColumn("new_col", lit(0))               |              df["new_col"]=1              |
|      重命名      |           df.withColumnRenamed("new_col", "zero")            | df.rename(columns={"merchant_id":"pid"})  |
|      列合并      | df.withColumn("concat", F.concat_ws("-", "zero", "sale_number")) |                                           |
|    df左右拼接    |        df.join(df_child, on=["goodsid"], how="left")         | df.merge(df_goods, on=["pid", "goodsid"]) |
|    df上下拼接    |                      df.union(df_child)                      |           df.concat([df1, df2])           |
|    分组、统计    |       df.groupby("storeid").agg({"sale_number":"max"})       |                                           |
|       排序       | df.sort(df_child.storeid.desc(), df_child.sale_number.asc()) |                                           |
| 和pandas相互转化 |                        df.toPandas()                         |  SQLContext(sc).createDataFrame(data=df)  |



