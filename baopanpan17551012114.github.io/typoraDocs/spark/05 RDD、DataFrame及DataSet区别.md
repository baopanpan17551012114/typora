# RDD、DataFrame和DataSet的区别

**在2.X中DataFrame=DataSet[Row],其实是不知道类型。**



## RDD和DataFrame

![1212395-24c42a0c0d81840f.webp](..\typora-user-images\1212395-24c42a0c0d81840f.webp.jpg)

**RDD：**

RDD[Person]虽然以Person为类型参数，但Spark框架本身不了解Person类的内部结构；

RDD是分布式的Java对象的集合；

**DataFrame:**

DataFrame提供了详细的结构信息，使得Spark SQL可以清楚地知道该数据集中包含哪些列，每列的名称和类型各是什么。DataFrame多了数据的结构信息，即schema；

DataFrame是分布式的Row对象的集合；

DataFrame除了提供了比RDD更丰富的算子以外，更重要的特点是提升执行效率、减少数据读取以及执行计划的优化，比如filter下推、裁剪等；

![img](https://www.pianshen.com/images/591/3a41c34637b333df2e2f32cb5db9f56f.png)

![img](https://www.pianshen.com/images/449/a860855bd49bf84d845bb50112f08111.png)



**RDD,DataFrame和DataSet三者转换图：**

![1395360-20200128220230063-1706902886](..\typora-user-images\1395360-20200128220230063-1706902886.jpg)



![image-20210817104857461](..\typora-user-images\image-20210817104857461.png)
