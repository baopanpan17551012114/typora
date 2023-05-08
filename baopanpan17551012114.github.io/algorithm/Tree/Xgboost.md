# 超详细解析XGBoost

https://zhuanlan.zhihu.com/p/562983875

XGBoost和GBDT的区别与联系：

1、GBDT是机器学习算法，XGBoost是该算法的工程实现；

2、在使用CART作为基分类器时，XGBoost显式的加入了正则化项，简化模型的同时有利于防止过拟合，从而提高模型泛化能力；

3、GBDT在训练时只使用了一阶导数信息，而XGBoost使用了二阶导信息；

4、传统的GBDT使用CART作为基分类器，而XGBoost支持多种类型的基分类器；

5、传统的GBDT在每轮迭代时使用全部数据，XGBoost则采用了与随机森林相似的策略，支持对数据进行采样；

6、XGBoost能够自动学习出缺失值的处理策略；

