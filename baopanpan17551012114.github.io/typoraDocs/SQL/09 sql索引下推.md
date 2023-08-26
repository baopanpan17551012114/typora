**一 什么是“索引条件下推”**

**索引条件下推”，称为** Index Condition Pushdown (ICP)，这是MySQL提供的用某**一个**索引对**一个**特定的表从表中获取元组”。

注意我们这里特意强调了“一个”，这是因为这样的索引优化不是用于多表连接而是**用于单表扫描**，确切地说，是**单表利用索引进行扫描以获取数据的一种方式**。

**二 “索引条件下推”的目的**

**用ySQL官方手册描述：**

The goal of ICP is to **reduce the number of full-record reads and thereby reduce IO operations**. For InnoDB clustered indexes, the complete record is already read into the InnoDB buffer. Using ICP in this case does not reduce IO.

这句官方描述，一是说明减少**完整记录（一条完整元组）读取的个数**；二是说明对于InnoDB聚集索引无效，只能是对SECOND INDEX这样的非聚集索引有效

**`ICP`索引条件下推的作用是什么**？ 

**一句话总结：索引条件下推`ICP`就是尽可量利用二级索引筛除不符合`where`条件的记录，如此一来减少需要回表继续判断的次数**

示例如下，这个例子来自`MySQL`官方文档： `Suppose`：假设这个表有联合索引`INDEX(zipcode, lastname, firstname)`

```mysql
SELECT * FROM people
  WHERE zipcode='95054'
  AND lastname LIKE '%etrunia%'
  AND address LIKE '%Main Street%';
```

- 不用`ICP`，只使用最左匹配原则。那么只能使用联合索引的`zipcode`，回表记录不能有效去除。
- 使用`ICP`，除了匹配`zipcode`的条件之外，额外匹配联合索引的`lastname`，看其是否符合`where`条件中的`'%etrunia%'`，然后进行回表。如此一来，使用联合索引就可以尽可量排除不符合`where`条件的记录。这就是`ICP`优化的真谛

![image-20210721201004391](..\typora-user-images\image-20210721201004391.png)

**再次总结，重要的事情多说几遍：`ICP`的实质就是通过二级索引尽可能的过滤不符合条件的记录，哪怕不符合最左匹配原则，减少回表，降低执行成本**



https://www.cnblogs.com/zengkefu/p/5684101.html

 **借用网上的2张图加以改造，并配以解释，来说明原理，更清晰地说明问题。**

**图一：不使用ICP技术（过程使用数字符号标示，如①②③等）**

![img](https://images2015.cnblogs.com/blog/99941/201607/99941-20160719113057888-330627393.png)



**过程解释：**

①：MySQL Server 发出读取数据的命令，调用存储引擎的索引读或全表表读。此处进行的是索引读。

②、③：进入存储引擎，读取索引树，在索引树上查找，把满足条件的（红色的）从表记录中读出（步骤 ④，通常有 IO）。

⑤：从存储引擎返回标识的结果。

以上，不仅要在索引行进行索引读取（通常是内存中，速度快。步骤 ③），还要进行进行步骤 ④，通常有 IO。

⑥：从存储引擎返回查找到的多条数据给 MySQL Server，MySQL Server 在 ⑦ 得到较多的元组。

⑦--⑧：依据 WHERE 子句条件进行过滤，得到满足条件的数据。

**注意在 MySQL Server 层得到较多数据，然后才过滤，最终得到的是少量的、符合条件的数据.**

**在不支持 ICP 的系统下，索引仅仅作为 data access 使用**。



**图二：使用ICP技术（过程使用数字符号标示，如①②③等）**

![img](https://images2015.cnblogs.com/blog/99941/201607/99941-20160719113114029-2069896261.png)

 

**过程解释：**

①：MySQL Server 发出读取数据的命令，过程同图一。

②、③：进入存储引擎，读取索引树，在索引树上查找，把满足已经下推的条件的（红色的）从表记录中读出（步骤 ④，通常有 IO）；

⑤：从存储引擎返回标识的结果。

此处，不仅要在索引行进行索引读取（通常是内存中，速度快。步骤 ③），还要在 ③ 这个阶段**依据下推的条件进行进行判断**，不满足条件的，不去读取表中的数据，直接在索引树上进行下一个索引项的判断，直到有满足条件的，才进行步骤 ④ ，这样，较没有 ICP 的方式，IO 量减少。

⑥：从存储引擎返回查找到的少量数据给 MySQL Server，MySQL Server 在 ⑦ 得到少量的数据。

因此比较图一无 ICP 的方式，返回给 MySQL Server 层的即是少量的、 符合条件的数据。

**在 ICP 优化开启时，在存储引擎端首先用索引过滤可以过滤的 where 条件，然后再用索引做 data access，被 index condition 过滤掉的数据不必读取，也不会返回 server 端**。



**四 实现细节**
**1 ICP只能用于辅助索引，不能用于聚集索引。**
**2 ICP只用于单表，不是多表连接是的连接条件部分（如开篇强调）**
**如果表访问的类型为：**
**3 EQ_REF/REF_OR_NULL/REF/SYSTEM/CONST: 可以使用ICP**
**4 range：如果不是“index tree only（只读索引）”，则有机会使用ICP**
**5 ALL/FT/INDEX_MERGE/INDEX_SCAN:  不可以使用ICP**




**五 上楼**

1 条件下推，一直是SQL优化的基本规则。所以，条件下推技术是常规技术。数据库的优化器几乎不会不实现条件下推优化。

2 技术层面，MySQL存在MySQL Server层和储存层，使得条件下推显得“有些割裂”。

3 非技术层面，MySQL之所以引入ICP，猜一猜或拍拍脑袋，原因你懂得。