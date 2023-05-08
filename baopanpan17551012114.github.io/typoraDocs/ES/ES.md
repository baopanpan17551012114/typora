## 创建index

```
PUT saas-algorithm-wscgoods-recommend-data-feature
{
	"settings": {
		"number_of_shards": 3,
		"number_of_replicas": 2
	},
	"mappings": {
	  "properties": {
	    "id": {
	      "type": "keyword"
  		},
  		"pid": {
  			"type": "long"
  		},
  		"goodsid": {
  			"type": "long"
  		},
  		"originVec": {
  			"type": "keyword"
  		}
	  }
	}
}
```

 

## 查询

```
GET saas-algorithm-xiaoke-information_data_index/_search
{
  "query": {
    "match_all": {
    }
  }
  , "sort": [
    {
      "createTime": {
        "order": "desc"
      }
    }
  ]
}
```

