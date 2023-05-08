## pyspark对Mysql数据库进行读写

https://zhuanlan.zhihu.com/p/136777424

### 读/写：

```python
# coding:utf-8
from pyspark import SparkConf
from pyspark.sql import SparkSession, SQLContext
import traceback
import os
os.environ['JAVA_HOME'] = "D:\\Java\\jdk1.8.0_05"

appname = "test"  # 任务名称
master = "local"  # 单机模式设置
conf = SparkConf().setAppName(appname).setMaster(master)  # 本地
spark = SparkSession.builder.config(conf=conf).getOrCreate()
sc = spark.sparkContext

def get_from_db():
        prop = {'user': '',
        'password': '',
        'driver': 'com.mysql.cj.jdbc.Driver'}
        url = 'jdbc:mysql://host:3306/database'
        df = spark.read.jdbc(url=url, table='alg_ec_recommend_sale_for_recall_storeid', properties=prop)
        return df.limit(100)

def df_to_mysql(df):
        prop = {'user': '',
                'password': '',
                'driver': 'com.mysql.cj.jdbc.Driver'}
        url = 'jdbc:mysql://host:3306/database'
        df.write.jdbc(url=url, table='new_test', mode='append', properties=prop)

if __name__ == '__main__':
    df = get_from_db()
    df.show(10)
    df_to_mysql(df)
```



## pyspark对hive进行读写

### 读hive:

```python
from pyspark import SparkConf
from pyspark.sql import SparkSession,HiveContext
import traceback
import os

spark = SparkSession.builder \
       .master("yarn") \
       .appName("test") \
       .enableHiveSupport()\
       .config("spark.driver.memory", "30g")\
       .config("spark.executor.instances", "1000")\
       .config("spark.executor.cores", "2")\
        .config("spark.executor.memory", "10g")\
       .getOrCreate()

sc = spark.sparkContext
hive_context= HiveContext(sc)
 
hive_read = "select * from data_model.wsc_recommend_sale_recall_storeid_with_more_info limit 20"
read_df = hive_context.sql(hive_read)
read_df.show(10)
```





## scala版本

### Driver所需依赖：

```java
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.38</version>
</dependency>
    
<!--ES相关依赖-->
<dependency>
    <groupId>org.elasticsearch</groupId>
    <artifactId>elasticsearch-spark-20_2.11</artifactId>
    <version>7.4.1</version>
</dependency>
```



### spark读hive、写MySQL：

```scala
import org.apache.spark.SparkConf
import org.apache.spark.sql.{DataFrame, Row, SaveMode, SparkSession}

object WscEmbedding {
  val conf = new SparkConf().setAppName("wscGoodsSaleWithMoreInfoOutput").set("spark.submit.deployMode", "client")
  val spark = SparkSession.builder.config(conf).enableHiveSupport().getOrCreate()
  import org.apache.spark.sql.functions._
  spark.sql("SET hive.mapred.supports.subdirectories=true")
  spark.sql("SET mapreduce.input.fileinputformat.input.dir.recursive=true")
  spark.sql("SET hive.execution.engine=tez")

  def pidSaleToDb(): Unit={
    val df = spark.sql("select * from data_model.wsc_recommend_recall_with_more_action")

    df.write.mode("overwrite").format("jdbc").option("url",
      "jdbc:mysql://host:3306/database").option("dbtable",
      "alg_ec_recommend_recall_with_more_action").option("user", "xxx").option("password",
      "xxx").option("batchsize", "10000").option("truncate", "true").save()
  }

  def main(args: Array[String]): Unit = {
    println("model train finish!")
    pidSaleToDb()

  }
}
```

### spark读MySQL：

```scala
object WscEmbedding {
  val conf = new SparkConf().setMaster("local").setAppName("wscGoodsEmbedding").set("spark.submit.deployMode", "client")
  val spark = SparkSession.builder.config(conf).enableHiveSupport().getOrCreate()
  import spark.implicits._

  spark.sql("SET hive.mapred.supports.subdirectories=true")
  spark.sql("SET mapreduce.input.fileinputformat.input.dir.recursive=true")

  val df = spark.read.format( "jdbc" ).options(
    Map( "url" -> "jdbc:mysql://host",
      "user" -> "xxx",
      "password" -> "xxx",
      "dbtable" -> "xiaoke_algorithm.alg_ec_search_expose_click_new" )).load().limit(500)
  println(df.count())
```



### spark读写ES：

```scala
import org.apache.spark.SparkConf
import org.apache.spark.sql.SparkSession

object SparkOpEs {
  val conf = new SparkConf().setMaster("local").setAppName("wscGoodsEmbedding").set("spark.submit.deployMode", "client")
  val spark = SparkSession.builder.config(conf).enableHiveSupport().getOrCreate()
  import spark.implicits._
  import org.elasticsearch.spark.sql._
  val options = Map(
    "es.nodes.wan.only" -> "true",
    "es.net.http.auth.user"->"",
    "es.net.http.auth.pass"->"",
    "es.nodes" -> "http://host:9200",
    "es.port" -> "9200",
    "es.mapping.id" -> "id"
  )

  /**
   * 从ES中读取
   */
  def readFromEs(): Unit ={
    val df = spark.read.format("es").options(options)
      .load("saas-algorithm-wscgoods-recommend-data-feature/_doc")
    df.show()
  }

  /**
   * 写入ES
   */
  def writeToEs(): Unit ={
    val df = List((1,1,1,1,1,1)).toDF("id","goodsEmbedding","goodsid",  "originVec","pid","topicDate")
    df.saveToEs("saas-algorithm-wscgoods-recommend-data-feature/_doc", options)
  }

  def main(args: Array[String]): Unit = {
    readFromEs()
  }
}
```



### scala JDBC方法：

```scala
import java.sql.{Connection, DriverManager, PreparedStatement}
import org.apache.spark.sql.DataFrame
import scala.collection.mutable.ListBuffer

object ScalaJDBC {
  val driver = "com.mysql.jdbc.Driver"
  val url = "jdbc:mysql://restore.saas-db-hkt3dfq1eldt6.mysql.internal.weimob.com?rewriteBatchedStatements=true"
  val username = "alg_sr_w"
  val password = "89xgpqOiwwdhFV!7KdQU"

  /**
   * 数据查询方法
   * @param sql
   * @return
   */
  def scalaPidSearch(sql: String): ListBuffer[(Long, Long, Float)]={
//    var list = List()
    val list = ListBuffer[(Long, Long, Float)]()
    var connection:Connection = null
    try {
      Class.forName(driver)
      connection = DriverManager.getConnection(url, username, password)
      val statement = connection.createStatement()
      val resultSet = statement.executeQuery(sql)
      while ( resultSet.next() ) {
        val pid = resultSet.getLong("pid")
        val goodsid = resultSet.getLong("goodsid")
        val sale = resultSet.getFloat("sale_number")
        list.append((pid, goodsid, sale))
      }
    } catch {
      case e: Throwable => e.printStackTrace

    }
    connection.close()
    list
  }

  def scalaStoreidSearch(sql: String): ListBuffer[(Long, Long, Float)]={
    //    var list = List()
    val list = ListBuffer[(Long, Long, Float)]()
    var connection:Connection = null
    try {
      Class.forName(driver)
      connection = DriverManager.getConnection(url, username, password)
      val statement = connection.createStatement()
      val resultSet = statement.executeQuery(sql)
      while ( resultSet.next() ) {
        val pid = resultSet.getLong("storeid")
        val goodsid = resultSet.getLong("goodsid")
        val sale = resultSet.getFloat("sale_number")
        list.append((pid, goodsid, sale))
      }
    } catch {
      case e: Throwable => e.printStackTrace
    }
    connection.close()
    list
  }

  /**
   * 更新方法，包括增删改
   * @param sql
   */
  def scalaIDU(sql:String): Unit ={
    var connection:Connection = null
    Class.forName(driver)
    connection = DriverManager.getConnection(url, username, password)
    val statement = connection.createStatement()

    val rs2 = statement.executeUpdate(sql)
    connection.close()
  }

  def insertBatch(sql:String, eles:Array[(Long, Long, Float,String)]): Unit = {
    var conn:Connection = null
    var ps:PreparedStatement = null
    try {
      conn = DriverManager.getConnection(url, username, password)
      ps = conn.prepareStatement(sql)
      var i = 1
      for(ele<-eles){
        ps.setLong(1, ele._1)
        ps.setLong(2, ele._2)
        ps.setFloat(3, ele._3)
        ps.setString(4, ele._4)
        ps.addBatch()
        i = i + 1
        if (i % 10000 == 0) { //2.执行batch
          ps.executeBatch()
          ps.clearBatch()
        }
      }
      ps.executeBatch()
      //提交数据
      conn.commit()
    } catch {
      case e: Exception =>
        e.printStackTrace()
    }
  }
  def main(args: Array[String]): Unit = {
//    val sql = "select * from xiaoke_algorithm.alg_ec_search_expose_click_new limit 1000;"
//    val li = scalaSearch(sql)
//    println(li)
    val tt = "aaa"
    val re = tt.split(",")
    println(re.length)
  }
}
```

