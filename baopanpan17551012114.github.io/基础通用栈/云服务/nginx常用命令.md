# nginx常用命令

**nginx -s reopen** #重启[Nginx](https://so.csdn.net/so/search?q=Nginx&spm=1001.2101.3001.7020)

**nginx -s reload** #重新加载Nginx配置文件，然后以优雅的方式重启Nginx

**nginx -s stop** #强制停止Nginx服务

**nginx -s quit** #优雅地停止Nginx服务（即处理完所有请求后再停止服务）

**nginx -t** #检测配置文件是否有语法错误，然后退出

**nginx -?,-h** #打开帮助信息

**nginx -v** #显示版本信息并退出

**nginx -V** #显示版本和配置选项信息，然后退出

**nginx -t** #检测配置文件是否有语法错误，然后退出

**nginx -T** #检测配置文件是否有语法错误，转储并退出

**nginx -q** #在检测配置文件期间屏蔽非错误信息

**nginx -p prefix** #设置前缀路径(默认是:/usr/share/nginx/)

**nginx -c filename** #设置配置文件(默认是:/etc/nginx/nginx.conf)

**nginx -g directives** #设置配置文件外的全局指令

**killall nginx** #杀死所有nginx进程

------

**Nginx在windows下常用命令：**

**启动：**
直接点击Nginx目录下的**nginx.exe** 或者 cmd运行**start nginx**

**关闭**
**nginx -s stop** 或者 **nginx -s quit**

stop表示立即停止nginx,不保存相关信息

quit表示正常退出nginx,并保存相关信息

**nginx -s stop** 或者 **nginx -s quit**

**nginx -s reload** ：修改配置后重新加载生效

**nginx -s reopen** ：重新打开日志文件

**nginx -t -c** /path/to/nginx.conf 测试nginx配置文件是否正确