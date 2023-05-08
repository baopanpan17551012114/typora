# [Elastic Search中DSL Query的常见语法](https://www.cnblogs.com/yucongblog/p/11973581.html)

query中语法：

```python
"query":{
	"match_all" : {},         # 全查询
  
  "match": {
      "FIELD": "TEXT"      # 单字段匹配，对输入进行分词处理后再去查询，部分命中的结果也会按照评分由高到低显示出来。
    },
  
  "match_phrase": {
      "FIELD": "PHRASE"    # 首先对查询条件做分词，目标文档需要包含分词后的所有词，还要保持这些词的相对顺序和文档中的一致

    },
  
  "match_phrase_prefix": {
      "FIELD": "PREFIX"    # 与match_phrase查询类似，但是会对最后一个Token在倒排序索引列表中进行通配符搜索
    },
  
  "term": {
      "FIELD": {
        "value": "VALUE"   # 精确匹配
      }
    },
  
  "terms": {
      "FIELD": [
        "VALUE1",         # 查询字段包含多个的时候使用terms(类似于sql中的in、or)
        "VALUE2"
      ]
    },
  
  
  "bool": {
                          # 组合条件
    }
  

}
```

bool - 用于组合多条件，相当于java中的布尔表达式。相当于最外层的括号，也就是完整的布尔表达式。
must - 必须符合要求， 相当于java中的逻辑运算符 ==或&&。在must范围内的条件都必须满足。
must_not - 必须不符合要求， 相当于java中的逻辑运算符 ！
should - 有任意条件符合要求即可，相当于java中的逻辑运算符 ||，在should范围内的条件，只要有任意一个满足即可。
range - 数学比较条件。计算数学范围的。



[boosting查询](http://www.elasticsearch.org/guide/en/elasticsearch/guide/current/not-quite-not.html#boosting-query)能够解决这个问题。它允许我们仍然将水果或者食谱相关的文档考虑在内，只是会降低它们的相关度 - 将它们的排序更靠后：

```bash
GET /_search
{
  "query": {
    "boosting": {
      "positive": {
        "match": {
          "text": "apple"
        }
      },
      "negative": {
        "match": {
          "text": "pie tart fruit crumble tree"
        }
      },
      "negative_boost": 0.5
    }
  }
}
```

它接受一个positive查询和一个negative查询。只有匹配了positive查询的文档才会被包含到结果集中，但是同时匹配了negative查询的文档会被降低其相关度，通过将文档原本的_score和negative_boost参数进行相乘来得到新的_score。

因此，negative_boost参数必须小于1.0。在上面的例子中，任何包含了指定负面词条的文档的_score都会是其原本_score的一半。