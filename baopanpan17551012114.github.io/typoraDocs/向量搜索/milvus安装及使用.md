1、安装

yum及docker安装：

https://blog.csdn.net/qq_26030541/article/details/104762327

如果为Ubuntu则直接跳到第六步：sudo apt-get install docker-ce

Milvus安装：

https://milvus.io/cn/docs/v1.1.1/milvus_docker-cpu.md

 

 

2、其它资料

https://milvus.io/cn/blogs/2019-11-08-data-management.md

 

参考手册:https://www.bookstack.cn/read/milvus-0.8/reference-data_manage.md

https://www.bookstack.cn/read/milvus-0.10.2-zh/aab034581e1522f5.md

 

官网：https://hub.docker.com/r/milvusdb/milvus

教程：https://www.runoob.com/docker/docker-mirror-acceleration.html

安装参考：

https://blog.csdn.net/NebulaDun/article/details/103544745?utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.control&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-1.control

 

启动命令：

```
sudo docker run -td --name milvus_1 \
-p 19530:19530 \
-p 19121:19121 \
-p 9091:9091 \
-v /data/hongguan.liu/milvus/db:/var/lib/milvus/db \
-v /data/hongguan.liu/milvus/conf:/var/lib/milvus/conf \
-v/data/hongguan.liu/milvus/logs:/var/lib/milvus/logs \
-v /data/hongguan.liu/milvus/wal:/var/lib/milvus/wal \
milvusdb/milvus:cpu-latest

sudo docker run -td --name milvus_1 \
-p 19530:19530 \
-p 19121:19121 \
-p 9091:9091 \
-v /data/milvus/db:/var/lib/milvus/db \
-v /data/milvus/conf:/var/lib/milvus/conf \
-v/data/milvus/logs:/var/lib/milvus/logs \
-v /data/milvus/wal:/var/lib/milvus/wal \
milvusdb/milvus:cpu-latest

sudo docker run -d --name milvus_gpu_1.1.1 --gpus all \
-p 19530:19530 \
-p 19121:19121 \
-v /home/$USER/milvus/db:/var/lib/milvus/db \
-v /home/$USER/milvus/conf:/var/lib/milvus/conf \
-v /home/$USER/milvus/logs:/var/lib/milvus/logs \
-v /home/$USER/milvus/wal:/var/lib/milvus/wal \
milvusdb/milvus:1.1.1-gpu-d061621-330cc6

```

 

```
sudo docker logs milvus_gpu_1.1.1
```

![image-20210806152522232](.\typora-user-images\image-20210806152522232.png)



java中使用文档：

https://milvus-io.github.io/milvus-sdk-java/javadoc/io/milvus/client/package-summary.html

 

## **配置MYSQL管理元数据**

https://milvus.io/cn/docs/v1.1.1/data_manage.md

第3、4步之间：

进入容器：

docker exec -it 62349aa31687 /bin/bash

62349aa31687为容器id

![img](file:///C:\Users\ADMINI~1\AppData\Local\Temp\ksohtml11020\wps2.jpg) 

进入mysql：

mysql -uroot -p

 

#### **重启milvus：**

关闭、移除及重启：

Docker stop milvus_cpu_1.1.0

Docker rm milvus_cpu_1.1.0

##### **确认运行状态：****sudo docker ps**

Ps:配置后的MySQL中放的不是数据，而是元数据（类似各种参数），如新建一个分区，需要保存相关信息

 

 

 

 