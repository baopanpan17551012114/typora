1、gunicorn开启

在项目的根目录下，输入下方的代码。

gunicorn --bind unix:/tmp/域名.socket 项目名称.wsgi:application
2、关闭和重启

首先执行如下命令获取Gunicorn进程树：

pstree -ap|grep gunicorn

之后输入如下指令关闭进程：

kill -9 1234

3、输入如下指令重启进程：

kill -HUP 1234