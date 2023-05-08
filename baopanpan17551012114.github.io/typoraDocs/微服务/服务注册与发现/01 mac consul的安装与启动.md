# mac consul的安装与启动

https://blog.csdn.net/kanglovejava/article/details/100622113



从https://www.consul.io/downloads.html下载mac64位压缩包

解压后把consul文件复制到/usr/local/bin目录下。
在该目录下执行consul命令，输出相关命令即可。

启动consul，执行命令consul agent -dev

启动后访问地址：
http://localhost:8500/ui/dc1/services
可以看到consul已经启动，health checks显示是正确的。

key/value中可以配置关于各个微服务中心的配置文件。
注意这个地方重启consul后key/value中的配置就会清空。
我每次在本地重启consul的时候，都要重新配置一遍各个中心的配置文件。



查看被占用端口

**lsof -i :8080**



# [MacOS中安装Consul（启动及关闭）](https://www.cnblogs.com/niceyoo/p/14223158.html)

https://www.cnblogs.com/niceyoo/p/14223158.html

# [python使用consul进行服务注册和发现](https://www.cnblogs.com/angelyan/p/11157176.html)

https://www.cnblogs.com/angelyan/p/11157176.html

