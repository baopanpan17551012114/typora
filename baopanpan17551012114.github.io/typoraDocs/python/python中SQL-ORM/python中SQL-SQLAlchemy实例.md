

实例类：

```python
# -*- coding:utf-8 -*-
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from sqlalchemy import Column, Integer, String
from sqlalchemy.ext.declarative import declarative_base

# app = Flask("")
# db = SQLAlchemy(app)

Base = declarative_base()


class Student(Base):
    __tablename__ = 'student'
    id = Column(Integer, primary_key=True)
    name = Column(String(100))
    age = Column(Integer)
    address = Column(String(100))
```

operation：

```python
#!/usr/bin/env python
# -*- coding:utf-8 -*-
from sqlalchemy import create_engine, MetaData, Table, Column, Integer, String
from sqlalchemy.orm import sessionmaker
from test_funcs.test_sqlalchemy.student import Student
from sqlalchemy import and_
from sqlalchemy import or_


class DbOperation(object):
    def __init__(self, name="root", passwd="Dada123456", host="localhost", db="test_tables"):
        self.engine = create_engine("mysql://{name}:{password}@{host}/{db}".format(
            name=name, password=passwd, host=host, db=db), echo=True)

    def sql_operations(self, sql=""):
        sql = '''CREATE TABLE `student` (
        `id` bigint(5) NOT NULL AUTO_INCREMENT,
        `name` varchar(15) DEFAULT NULL,
        `age` int(3) DEFAULT NULL,
        `address` varchar(50) DEFAULT NULL,
        PRIMARY KEY (`id`)
        ) ENGINE=InnoDB;
        '''
        conn = self.engine.connect()
        conn.execute(sql)
        conn.commit()
        # self.engine.connect()  # #表示获取到数据库连接。类似我们在MySQLdb中游标course的作用。

    def orm_operations(self):
        """创建table"""
        metadata = MetaData(self.engine)
        student = Table('student_new', metadata,
                        Column('id', Integer, primary_key=True),
                        Column('name', String(50), ),
                        Column('age', Integer),
                        Column('address', String(10)),
                        )
        metadata.create_all(self.engine)

    def orm_insert(self):
        """增"""
        DBsession = sessionmaker(bind=self.engine)  # type: sessionmaker
        session = DBsession()
        student1 = Student(id=1001, name='ling', age=25, address="beijing")
        student2 = Student(id=1002, name='molin', age=18, address="jiangxi")
        student3 = Student(id=1003, name='karl', age=16, address="suzhou")
        session.add_all([student1, student2, student3])
        session.commit()
        session.close()

    def orm_select(self):
        """查"""
        DBSession = sessionmaker(bind=self.engine)
        session = DBSession()
        # my_stdent = session.query(Student).filter_by(name="ling").first()

        # equals:
        stdent_list = session.query(Student).filter(Student.id == 1001).all()
        print(stdent_list)

        # not equals:
        my_stdent = session.query(Student).filter(Student.id != 1001)

        # LIKE:
        my_stdent = session.query(Student).filter(Student.name.like("in"))

        # IN:
        my_stdent = session.query(Student).filter(Student.name.in_(['feng', 'xiao', 'qing']))
        # not in
        my_stdent = session.query(Student).filter(~Student.name.in_(['feng', 'xiao', 'qing']))

        # AND:
        my_stdent = session.query(Student).filter(and_(Student.name == 'fengxiaoqing', Student.id == 10001))

        # 或者
        my_stdent = session.query(Student).filter(Student.name == 'fengxiaoqing').filter(Student.address == 'chengde')

        # OR:
        my_stdent = session.query(Student).filter(or_(Student.name == 'fengxiaoqing', Student.age == 18))

    def orm_update(self):
        """改"""
        DBSession = sessionmaker(bind=self.engine)
        session = DBSession()

        my_stdent = session.query(Student).filter(Student.id == 1002).first()
        my_stdent.name = "baopanpan"
        my_stdent.address = "maoliqiusi"
        session.commit()
        student1 = session.query(Student).filter(Student.id == 1002).first()
        print(student1.name, student1.address)

    def orm_delete(self):
        """删"""
        DBSession = sessionmaker(bind=self.engine)
        session = DBSession()

        session.query(Student).filter(Student.id == 1001).delete()
        session.commit()
        session.close()

    def orm_other_operations(self):
        """统计、分组、排序"""
        DBSession = sessionmaker(bind=self.engine)
        session = DBSession()

        count = session.query(Student).filter(Student.name.like("%ao%")).count()
        print(count)
        group = session.query(Student).group_by(Student.age)
        print(group)
        ord_desc = session.query(Student).filter(Student.name.like("ba%")).order_by(Student.id.desc()).all()
        for i in ord_desc:
            print(i.id)


if __name__ == '__main__':
    db = DbOperation()
    db.orm_other_operations()
```

