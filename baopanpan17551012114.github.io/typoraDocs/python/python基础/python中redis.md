# [redis在mac上的下载安装](https://www.cnblogs.com/yanguobin/p/11446967.html)

https://www.cnblogs.com/yanguobin/p/11446967.html

[redis官网](https://redis.io/)下载压缩包：

![img](/Users/baopanpan/baopanpan17551012114.github.io/typoraDocs/typora-user-images/1178951-20190902150559881-867538310.png)

在终端进入下载后的目录，然后：

- 解压：tar zxvf redis-5.0.5.tar.gz
- 移动到：sudo mv redis-5.0.5 /usr/local
- 切换到：cd /usr/local/redis-5.0.5/
- 编译测试：make test
- 编译安装：make install

完成安装

要使用redis，先开启redis服务端，在终端输入redis-server，如下：

![img](https://img2018.cnblogs.com/blog/1178951/201909/1178951-20190902152925304-1110252635.png)

可以看到redis服务端默认在6379端口成功开启，不要关闭此窗口，并重新打开一个终端，输入redis-cli，打开redis客户端：

 ![img](https://img2018.cnblogs.com/blog/1178951/201909/1178951-20190902153253644-1629307406.png)

然后就可以在redis客户端交互式的使用redis的一些命令了

在redis客户端输入shutdown命令可以关闭redis服务端：

![img](https://img2018.cnblogs.com/blog/1178951/201909/1178951-20190902154018237-1920221392.png)

![img](https://img2018.cnblogs.com/blog/1178951/201909/1178951-20190902153932788-1342752014.png)