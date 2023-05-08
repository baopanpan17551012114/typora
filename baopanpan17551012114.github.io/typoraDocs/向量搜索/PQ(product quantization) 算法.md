## Product Quantization(乘积量化) 算法

https://blog.csdn.net/u010368556/article/details/80960661

http://vividfree.github.io/%E6%9C%BA%E5%99%A8%E5%AD%A6%E4%B9%A0/2017/08/05/understanding-product-quantization



https://shenchunxu.blog.csdn.net/article/details/102881474?utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-2.essearch_pc_relevant&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromMachineLearnPai2%7Edefault-2.essearch_pc_relevant



**Faiss中的IVF和PQ原理  https://zhuanlan.zhihu.com/p/356373517**

# [乘积量化（Product Quantization）](https://www.cnblogs.com/mafuqiang/p/7161592.html)

乘积量化

1、简介

　　乘积量化（PQ）算法是和VLAD算法是由法国INRIA实验室一同提出来的，为的是加快图像的检索速度，所以它是一种检索算法，在矢量量化（Vector Quantization，VQ）的基础上发展而来，虽然PQ不算是新算法，但是这种思想还是挺有用处的，本文没有添加公式。

　　它原文中是接在VLAD算法后面，假设我们使用VLAD算法获得了1M的图像表达向量，向量的维度为D=128，则对于一幅查询图像来说，我们需要计算1M个余弦距离，这样实时性就比较差。所以如何加快这种距离的计算速度就是PQ算法所要完成的任务。当然为了解决这个问题，已经有很多算法被提出了，如KDTree，LSH，ITQ等都是为解决这个问题而提出的，属于KNN或ANN范畴。

2、空间切分

　　首先，PQ先将D维空间切分成M份：即将128维空间切分成M个D/M维的子空间，如下图所示M=8（在原文中，作者由于是在PCA之后进行的PQ检索，所以进行了一个随机旋转，因为PCA之后特征值的顺序是按照从大到小排列的）

![img](..\typora-user-images\1138886-20170713165102025-150593021.png)



# Faiss中的IVF和PQ原理

https://zhuanlan.zhihu.com/p/356373517

![img](..\typora-user-images\v2-a625c04e4b7737eea4fde21e18b2aed7_720w.jpg)

![img](..\typora-user-images\v2-094f80b69629541c0dd59a37d4bec5c8_720w.jpg)