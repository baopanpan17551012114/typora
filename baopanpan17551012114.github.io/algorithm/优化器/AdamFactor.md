苏剑林. (Mar. 23, 2020). 《AdaFactor优化器浅析（附开源实现） 》[Blog post]. Retrieved from https://spaces.ac.cn/archives/7302

https://spaces.ac.cn/archives/7302#how_to_cite

## Adam

首先我们来回顾一下常用的Adam优化器的更新过程。设t为迭代步数，αt为当前学习率，L(θ)是损失函数，θ是待优化参数，ϵ则是防止溢出的小正数，那么Adam的更新过程为:

![image-20230628143650842](/Users/baopanpan/Library/Application Support/typora-user-images/image-20230628143650842.png)

