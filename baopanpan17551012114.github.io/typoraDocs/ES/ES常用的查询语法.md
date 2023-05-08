# ES常用的查询语法

https://www.jianshu.com/p/0e503c6dcf89

###### 1、query string search

GET /index/type/_search?q=name:zhangsan&sort=age:desc

###### 2、query DSL(Domain Sepcified Language)

1）查询所有结果



```bash
GET /index/type/_search
{
    "query":{"match_all":{}}
}
```

2）根据条件查询

```bash
GET /index/type/_search
{
    "query":{
          "match":{
                    "name":"zhangsan"
          }
    },
    "sort":[
        {
             "age":"desc"
       }
   ]
}
```

3）分页查询

```bash
GET /index/type/_search
{
    "query":{"match_all":{}},
    "from":1,
    "size":2
}
```

4）指定查询结果的字段

```bash
GET /index/type/_search
{
    "query":{"match_all":{}},
    "_source":["name","age"]
}
```

###### 3、query filter

```bash
GET /index/type/_search
{
    "query":{
          "bool":{
                    "must":{
                             "match":{
                                   "name":"zhangsan"
                             }
                     },
                     "filter":{
                              "range":{
                                      "age":{"gt":25}
                             }
                    }
          }
    },
    "sort":[
        {
             "age":"desc"
       }
   ]
}
```

###### 4、full-text search

```bash
GET /index/type/_search
{ 
    "query":{
          "match":{
                  "name":"zhangsan"
            }              
    } 
}
```

###### 5、phrase search(短语搜索：完全匹配)

```bash
GET /index/type/_search
{ 
    "query":{
          "match_phrase":{
                  "name":"zhangsan"
            }              
    } 
}
```

###### 6、highlight search(高亮搜索)

```bash
GET /index/type/_search
{ 
    "query":{
          "match_phrase":{
                  "name":"zhangsan"
            }              
    } ,
   "highlight":{
              "fields":{
                    "name":{}
               }
   }
}
```

