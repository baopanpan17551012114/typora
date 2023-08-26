https://mp.weixin.qq.com/s?__biz=MzIwMDgzMjc3NA==&mid=2247484489&idx=1&sn=b4078d168dfe86d992a5eca26b1e4f4b&chksm=96f66620a181ef362a285dcfb06dedcc07c4ef93edc6784c3466568e2eb4715ac471467dec42&scene=21#wechat_redirect

一条查询SQL执行流程图如下

![图片](https://mmbiz.qpic.cn/mmbiz_png/SYoYmIOcI5pN3fuFqya0J40LO5XlHKOZT4ibbWQvqnbeZtbRZwTdG2jG993B2cMLE90fIQjV1MSt61wWfx1WZ0A/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

## 查询缓存（Mysql8.0后取消）

查询缓存，它其实是一个哈希表，它将执行过的语句及其结果会以 key-value 对的形式，被直接缓存在内存中。

它的key是一个哈希值，是通过查询SQL、当前要查询的数据库、客户端协议版本等，生成的一个哈希值，而它的value自然就是查询结果啦。

查询缓存极其难用，因为

- 只要有对一个表的更新，这个表上所有的查询缓存都会被清空
- SQL任何字符上的不同,如空格,注释,都会导致缓存不命中

因此，我能想到用查询缓存的表，只有一种情况，那就是配置表。其他的业务表，根本是无法利用查询缓存的特性，或许Mysql团队也是觉得查询缓存的使用场景过于局限，就无情的将它剔除。

![image-20210721180154275](..\typora-user-images\image-20210721180154275.png)



## 分析器（这里将解析器和预处理器统一称为分析器）

### 解析器

**词法分析**

```
select username from userinfo
```

先进行词法分析，从左到右一个字符、一个字符地输入，然后根据构词规则识别单词。将会生成4个Token,如下所示。"

![图片](https://mmbiz.qpic.cn/mmbiz_png/SYoYmIOcI5pN3fuFqya0J40LO5XlHKOZCTl7I0B37v9GYjrEQTYlsB1x5VArSgutrJ65O0ta410aWMzA3nCAuQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

**语法分析**

进行语法解析，判断你输入的这个 SQL 语句是否满足 MySQL 语法。然后生成下面这样一颗语法树。"

![图片](https://mmbiz.qpic.cn/mmbiz_png/SYoYmIOcI5pN3fuFqya0J40LO5XlHKOZJopx8mKrhYCkCx2pZbkjWweLEtWWnuQL7uU0X1Ipn9tTW4CW4ynUlw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

如果语法不对，会收到一个提示如下：

```
You have an error in your SQL syntax
```

### **预处理器**

检查列名对不对，数据库的这张表里是不是真的有这个列。再看看表名对不对，如果不对，会看到下面的错误！"

```
Unknown column xxx in ‘where clause’
```

最后做**权限验证**，如果你没有操作这个表的权限，会报下面这个错误：

```
ERROR 1142 (42000): SELECT command denied to user 'root'@'localhost' for table 'xxx'
```

（这个地方，大家可能有疑问，因为有些文章说是执行器做的权限验证，可以直接拉到本文底部看说明）

**最后，这颗语法树会传递给优化器**。



## 优化器

针对语法树进行优化；

判断一下怎么样执行更快，比如先查`Table1`再查`Table2`，还是先查`Table2`再查`Table1`呢？判断完如何执行以后，生成执行计划；

然后交给执行器。

SQL优化器的具体模块参考如下图：

![img](https://pic1.zhimg.com/80/v2-7c9d937de6b24190c2978f0a5854b9fc_720w.jpg)

**采用代价公式，判断究竟是利用全表扫描还是利用索**引

**确定JOIN顺序**

对于有N个表JOIN的查询，实际有N！种可能，如何快速确定采用哪种方法最优，MySQL采用称之为“贪婪算法”的策略，尽可能找到最优路径；

**索引条件下推**

当查询条件都为索引列时，索引条件下推能够将索引条件直接作用于索引上。



### 执行器

根据执行计划来进行执行查询。根据指令，逐条调用底层存储引擎，逐步执行。

`MySQL`定义了一系列抽象存储引擎API，以支持插件式存储引擎架构。Mysql实现了一个抽象接口层，叫做 `handler(sql/handler.h)`，其中定义了接口函数，比如：`ha_open`, `ha_index_end`, `ha_create`等等，存储引擎需要实现这些接口才能被系统使用。



## 最后

最后一个阶段，Mysql会将查询结果返回客户端。
唯一需要说明的是，如果是SELECT类型的SQL，Mysql会将查询结果缓存起来。至于其他的SQL，就将该表涉及到的查询缓存清空。