# ORM 实例教程

http://www.ruanyifeng.com/blog/2019/02/orm-tutorial.html

面向对象编程和关系型数据库，都是目前最流行的技术，但是它们的模型是不一样的。

面向对象编程把所有实体看成对象（object），关系型数据库则是采用实体之间的关系（relation）连接数据。很早就有人提出，关系也可以用对象表达，这样的话，就能使用面向对象编程，来操作关系型数据库。

**简单说，ORM 就是通过实例对象的语法，完成关系型数据库的操作的技术，是"对象-关系映射"（Object/Relational Mapping） 的缩写。**

ORM 把数据库映射成对象。

> - 数据库的表（table） --> 类（class）
> - 记录（record，行数据）--> 对象（object）
> - 字段（field）--> 对象的属性（attribute）

举例来说，下面是一行 SQL 语句。

> ```javascript
> SELECT id, first_name, last_name, phone, birth_date, sex
>  FROM persons 
>  WHERE id = 10
> ```

程序直接运行 SQL，操作数据库的写法如下。

> ```javascript
> res = db.execSql(sql);
> name = res[0]["FIRST_NAME"];
> ```

改成 ORM 的写法如下。

> ```javascript
> p = Person.get(10);
> name = p.first_name;
> ```

一比较就可以发现，ORM 使用对象，封装了数据库操作，因此可以不碰 SQL 语言。开发者只使用面向对象编程，与数据对象直接交互，不用关心底层数据库。

总结起来，ORM 有下面这些优点。

> - 数据模型都在一个地方定义，更容易更新和维护，也利于重用代码。
> - ORM 有现成的工具，很多功能都可以自动完成，比如数据消毒、预处理、事务等等。
> - 它迫使你使用 MVC 架构，ORM 就是天然的 Model，最终使代码更清晰。
> - 基于 ORM 的业务代码比较简单，代码量少，语义性好，容易理解。
> - 你不必编写性能不佳的 SQL。

但是，ORM 也有很突出的缺点。

> - ORM 库不是轻量级工具，需要花很多精力学习和设置。
> - 对于复杂的查询，ORM 要么是无法表达，要么是性能不如原生的 SQL。
> - ORM 抽象掉了数据库层，开发者无法了解底层的数据库操作，也无法定制一些特殊的 SQL。



