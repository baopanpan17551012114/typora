# Spark/PySpark中map与flatMap

map将函数作用到数据集的每一个元素上，生成一个新的分布式的数据集(RDD)返回;

即：输入Row，返回RDD

```python
def get_recall_type(line):
    re = ""
    try:
        json_result = json.loads(line["recid"])
        if(line["label"]=="click"):
            re = json_result.get(str(line["goodsid"]))
        else:
            re = "expose_none"
    except:
        re = "expose_none"
    if(re==None):
        re = "None"
    return {"wid":line["wid"], "merchant_id":line["merchant_id"], "label":re}
    
df.rdd.map(get_recall_type).toDF().show()
```

flatMap会先执行map的操作，再将所有对象合并为一个对象，返回值是一个Sequence;

即：传入Row，map处理后，合并为一个list

```python
def get_recall_type(line):
    re = ""
    try:
        json_result = json.loads(line["recid"])
        if(line["label"]=="click"):
            re = json_result.get(str(line["goodsid"]))
        else:
            re = "expose_none"
    except:
        re = "expose_none"
    if(re==None):
        re = "None"
    # return {"wid": line["wid"], "merchant_id": line["merchant_id"], "label": re}
    return [re]
    
co = df.rdd.flatMap(get_recall_type).collect()
print(co)
```

