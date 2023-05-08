

在派单过滤日志中，根据指定过滤原因、订单id、骑士id和订单类型，查询订单过滤时具体信息，如手头订单等；

```elixir
POST bizlog10259-20211107/_search
{
  "query": {
    "bool": {
      "must": [
        {"term": {
          "filter_step": {
            "value": "filter_order_limit_transporter_inshop_assign"
          }
        }},{
          "term": {
            "order_id": {
              "value": "302966830794019"
            }
          }
        },{
          "term": {
            "transporter_id": {
              "value": "20800793"
            }
          }
        },{
          "term": {
            "type": {
              "value": "inshop_assign"
            }
          }
        }
      ]
    }
  }
}
```

该查询在es的discover中同样：

![image-20211109152025840](../typoraDocs/typora-user-images/image-20211109152025840.png)





根据派单成功日志，查询订单所属taskid;

```
POST bizlog10144-20211107/_search
{
  "_source": ["user_id", "dispatch_time", "task_id"], 
  "query": {
    "bool": {
      "must": [
        {"term": {
          "type": {
            "value": "inshop_assign"
          }
        }},{
          "term": {
            "order_id": {
              "value": "302966348976902"
            }
          }
        }
      ]
    }
  }
}
```

