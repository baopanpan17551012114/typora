## 1. 为什么要有事务

事务广泛的运用于订单系统、银行系统等多种场景

例如：

> A用户和B用户是银行的储户，现在A要给B转账500元，那么需要做以下几件事：
>
> 1. 检查A的账户余额>500元；
> 2. A 账户中扣除500元;
> 3. B 账户中增加500元;

正常的流程走下来，A账户扣了500，B账户加了500，皆大欢喜。

那如果A账户扣了钱之后，系统出故障了呢？A白白损失了500，而B也没有收到本该属于他的500。

以上的案例中，隐藏着一个前提条件：A扣钱和B加钱，要么同时成功，要么同时失败。事务的需求就在于此

**所谓事务,它是一个操作序列，这些操作要么都执行，要么都不执行，它是一个不可分割的工作单位**。

例如，银行转帐工作：从一个帐号扣款并使另一个帐号增款，这两个操作要么都执行，要么都不执行。所以，应该把他们看成一个事务。事务是数据库维护数据一致性的单位，在每个事务结束时，都能保持数据一致性。

## 事务四大特性(简称ACID)

- 原子性(Atomicity)：所有语句作为一个单元全部成功执行或全部取消。
- 一致性(Consistency)：如果数据库在事务开始时处于一致状态，则在执行该事务期间将保留一致状态。
- 隔离性(Isolation)：事务之间不相互影响。
- 持久性(Durability)：事务成功完成后，所做的所有更改都会准确地记录在数据库中。所做的更改不会丢失

## [事务控制语句](https://www.cnblogs.com/cjaaron/p/9215023.html)

## 语法

```
commit； 提交（确认操作，写到硬盘上）
rollback； 回滚（回退）
savepoint； 保存点名
rollback to；  回滚（回退）到某个点
```



## MySQL中事务的五种分类 

## https://blog.csdn.net/qq_41333582/article/details/84196964

从事务理论的角度来看，可以把事务分为以下五种类型：

扁平事务(Flat Transactions)

带有保存点的扁平事务(Flat Transactions with Savepoints)

链事务(Chained Transactions)

嵌套事务(Nested Transactions)

分布式事务(Distributed Transactions)




## 事务控制的注解实现（java）



## 事务控制的代码实现

https://blog.csdn.net/m0_47298981/article/details/111284067

- JDBC方式的事务控制

```java
package com.lty;
import java.sql.*;
/**
 * @Description:
 * @Author: Feng.QC
 * @Date: 2020/12/16 11:33
 * @Version: 1.0
 */
public class ConnectionTransaction {
    public static void main(String[] args) {
        Connection conn = null;
        Statement stat = null;
        PreparedStatement prep1 = null;
        PreparedStatement prep2=null;
        try {
            //注册驱动
            Class.forName("com.mysql.jdbc.Driver");
            String url = "jdbc:mysql://localhost:3306/test";
            String username = "root";
            String password = "root";
            //开启事务
            conn.setAutoCommit(false);
            //创建数据库的连接
            conn = DriverManager.getConnection(url, username, password);
            //sql语句---实现A给B转账
            String sql1="update  account set money=money-? where id=?";
            String sql2="update account set money=money+? where id=?";
            //预处理
            prep1 = conn.prepareStatement(sql1);
            prep2 = conn.prepareStatement(sql2);
            //赋值
            prep1.setInt(1,100);
            prep1.setInt(2,1);
            prep2.setInt(1,100);
            prep2.setInt(2,2);
            prep1.executeUpdate();
            /*
            * int i=3/0;
            *
            * Exception in thread "main" java.lang.NullPointerException
	        * at com.lty.ConnectionTransaction.main(ConnectionTransaction.java:24)
            * 在一个事务中，出现异常的话，将会回滚，对数据库中的数据没有进行改变。
            * */
            prep2.executeUpdate();
            conn.commit();

        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
            try {
                conn.rollback();
            } catch (SQLException ex) {
                ex.printStackTrace();
            }
        }
        //释放空间
        if (prep1!=null){
            try {
                prep2.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if (prep2!=null){
            try {
                prep2.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if (stat!=null){
            try {
                stat.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        if (conn!=null){
            try {
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```



## 事务控制python实现

```python3
#coding=utf-8
import sys
import MySQLdb

class TransferMoney(object):
    def __init__(self,conn):
        self.conn = conn

    #检查账户是否合法
    def check_acct_avaiable(self,acctid):
        cursor = self.conn.cursor()
        try:
            sql = "select * from account where acctid=%s" % acctid
            cursor.execute(sql)
            print "check account:" + sql
            rs = cursor.fetchall()
            if len(rs) != 1:
                raise Exception("account %s illega" % acctid)
        finally:
            cursor.close()

    #检查是否有足够的钱
    def has_enough_money(self,acctid,money):
        cursor = self.conn.cursor()
        try:
            sql = "select * from account where acctid=%s and money > %s" % (acctid,money)
            cursor.execute(sql)
            print "has enough money:" + sql
            rs = cursor.fetchall()
            if len(rs) != 1:
                raise Exception("account %s not enough money" % acctid)
        finally:
            cursor.close()

    #账户减钱
    def reduce_money(self,acctid,money):
        cursor = self.conn.cursor()
        try:
            sql = "update account set money = money-%s where acctid = %s" % (money,acctid)
            cursor.execute(sql)
            print "reduce_money:" + sql
            if cursor.rowcount != 1:
                raise Exception("reduce money fail %s" % acctid)
        finally:
            cursor.close()

    #账户加钱
    def add_money(self,acctid,money):
        cursor = self.conn.cursor()
        try:
            sql = "update account set money = money+%s where acctid = %s" % (money,acctid)
            cursor.execute(sql)
            print "add_money:" + sql
            if cursor.rowcount != 1:
                raise Exception("add money fail %s" % acctid)
        finally:
            cursor.close()
    #主执行语句
    def transfer(self,source_acctid,target_acctid,money):
        try:
            self.check_acct_avaiable(source_acctid)
            self.check_acct_avaiable(target_acctid)
            self.has_enough_money(source_acctid,money)
            self.reduce_money(source_acctid,money)
            self.add_money(target_acctid,money)
            self.conn.commit()
        except Exception as e:
            self.conn.rollback()
            raise e

if __name__ == "__main__":
    source_acctid = sys.argv[1]
    target_acctid = sys.argv[2]
    money = sys.argv[3]
    conn = MySQLdb.Connect(host = '127.0.0.1',port=3306,user='root',passwd='',db='test',charset='utf8')
    tr_money = TransferMoney(conn)

    try:
        tr_money.transfer(source_acctid,target_acctid,money)
    except Exception as e:
        print "Happen:" + str(e)
    finally:
        conn.close()
```

