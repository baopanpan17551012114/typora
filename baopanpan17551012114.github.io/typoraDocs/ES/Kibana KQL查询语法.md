# Kibana KQL查询语法

https://blog.csdn.net/jy02268879/article/details/119889326

### 一、KQL简单介绍

KQL（Kibana Query Language），也就是在Kibana上面进行查询时使用的语法。

Kibana中也可以使用Lucene的查询语法。

KQL可以参考https://www.elastic.co/guide/en/kibana/current/kuery-query.html#_terms_query

### 二、KQL查询语法

#### 1.Terms Query

说人话就是根据列名查那一列的内容

比如说我收集的日志内容都在列message中的。

现在我要查询日志带有select字符的。那就是

`message:select`
上面这个表达式，会查询出message字段中包含select的文档对象，注意是包含，包含的是select这一个词

但是selection是不会被查出来的。这里查的都是词！词！

这个com.sid.controller.TestController.select也不会被查出来，默认是用空格分词的，不会用.分词。

 引号的使用

`message:hello word`
这个是搜索message中包含hello，或者包含world，或者两者都包含的情况。

如果只需要把整个hello word一起查询出来。则使用引号

`message:"hello word"`

#### 2.Boolean queries

**KQL支持or   and  not查询**

其中and的优先级高于or

`response:200 or extension:php`
 匹配response列有200，或者extension列有php的内容

`response:200 and extension:php`
  匹配response列有200，并且extension列有php的内容

`response:(200 or 404)`
 匹配response列有200或者404的内容

`response:200 and (extension:php or extension:css)`
  匹配response列有200，并且extension列有php或者css的内容 

`response:200 and extension:php or extension:css`
  匹配response列有200并且extension列有php的内容。或者extension列有css的内容  

`not response:200`
 匹配response列没有200的内容

`response:200 and not (extension:php or extension:css)`
匹配response列有200，并且extension列没有php、css的内容

`tags:(success and info and security)`
匹配tags列有success并且有info并且有security的内容

#### 3.Range queries

**KQL支持对数字和日志列使用 <   <=  >  >=**

`account_number >= 100 and items_sold <= 200`

#### 4.Date range queries

`@timestamp < "2021-01-02T21:55:59"`

`@timestamp < "2021-01"`

`@timestamp < "2021"`

#### 5.Exist queries

response:*
匹配所有response字段存在的文档（无论其值为多少） 

#### 6.Wildcard queries

`machine.os:win*`
匹配machine.os字段中以win开头的内容 

`machine.os*:windows 10`
这是匹配了多个字段，匹配了以machine.os字段开头的所有字段里面有windows 10的内容

#### 字段嵌套查询

首先准备一个多层的数据，比如下面的这几条数据。

```
{
  "level1": [
    {
      "level2": [
        {
          "prop1": "foo",
          "prop2": "bar"
        },
        {
          "prop1": "baz",
          "prop2": "qux"
        }
      ]
    }
  ]
}
```

比如想筛选 level1.level2.prop1 是 foo 或者是 baz的，可以这样写：

```
level1.level2 {prop1: "foo" or prop1: "baz"}
```

## 总结

KQL是一个比较简单筛选数据的查询语言，包括条件、逻辑、多层查询等用法，能辅助报表的制作和实时日志的筛选。