# python flask中config配置管理问题

https://blog.csdn.net/m0_38124502/article/details/78670161

在项目中我们需要配置各种环境。
如果我们的配置项很少的话，
可以直接简单粗暴的来；
比如：

```python
app =Flask(__name__)
app.config['DEBUG']=True
```

app.config其实是实例化了flask.config.Config类的实例，
继承于python内置数据结构dict字典，可以使用update方法：

```python
app.config.update(
DEBUG=true,
SECRET_KEY='xxxx'
)
```

如果设置很多的情况下，想要集中起来管理设置项，
应该将他们存放在一个文件里面。
app.config支持很多的配置方式。
比如现在我们有叫settings.py的配置文件，里面的内容是
sss=yy
我们可以有三种方式加载。
1）使用配置文件进行加载

```python
app.config.from_object('settings.py')#使用模块的名字
#也可以在引用之后直接传入对象
import settings
app.config.from_object(settings)
```

2）使用文件名字加载。直接传入名字就行了
别的后缀的也可以，不局限于.py的

```python
app.config.from_pyfile('settings.py',silent=True)
#默认当配置文件不存在的时候抛出异常，
#使用silent=True的时候只是会返回False,但是不抛出异常
```

3）使用环境变量加载。这种方法依然支持silent参数，获得路径后其实
还是使用from_pyfile的方式加载的。

```python
$ export YOURAPPLICATION_SETTINGS='settings.py'
app.config.from_envvar('YOURAPPLICATION_SETTINGS')
```



### 实例：

```python
from core import config

app = Flask('dos')
app.config.from_object(config)
app.config['__APP_NAME__'] = config.APP_NAME
register_name = config.APP_REGISTER_NAME or config.APP_NAME
```

core包下config包下      __init__.py

```python
# -*- coding: utf-8 -*-

import os
from .default import *  # 引入更多配置参数

try:
    from .local import *
except ImportError:
    pass

APP_ENV = os.getenv('APP_ENV', 'LOCAL')
APP_REGISTER_NAME = os.getenv('APP_REGISTER_NAME')

if APP_ENV != 'LOCAL':
    ZK_HOSTS = os.getenv('ZK_HOSTS')
    CFG_SERVICE = True
    CFG_REDIS = os.getenv('CFG_REDIS')
    CFG_USE_LOCAL = False

```

core包下config包下 default.py:

```python
# -*- coding: utf-8 -*-
import os

PROJECT_PATH = os.path.abspath(
    os.path.join(
        os.path.abspath(os.path.dirname(__file__)),
        os.pardir, os.pardir))

APP_NAME = __APP_NAME__ = 'dos'

DEBUG = False

PROFILE = False

SQLALCHEMY_TRACK_MODIFICATIONS = True

SQLALCHEMY_POOL_RECYCLE = 300

SQLALCHEMY_DATABASE_URI = 'mysql://dev_w:6nvjq0_HW@10.9.113.30:3306/dos_db'

SQLALCHEMY_BINDS = {
    'dada_slave': 'mysql://dev_readonly:6nvjq0_H@10.9.113.30:3306/dos_db',
    'dada_express': 'mysql://dev_readonly:6nvjq0_H@10.9.113.30:3306/dada',
    'dada_express_slave': 'mysql://dev_readonly:6nvjq0_H@10.9.113.30:3306/dada',
    'dw_api_db': 'mysql://dev_readonly:6nvjq0_H@10.9.113.30:3306/dw_api',
    'dw_api_db_slave': 'mysql://dev_readonly:6nvjq0_H@10.9.113.30:3306/dw_api',
}
```

