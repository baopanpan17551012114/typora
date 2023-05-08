```py
sudo CFLAGS=-stdlib=libc++ pip install python-geohash
```

https://zhuanlan.zhihu.com/p/94744929

2.conda常用的命令

1）查看安装了哪些包

```text
conda list
```

2)查看当前存在哪些虚拟环境

```text
conda env list 
conda info -e
```

3)检查更新当前conda

```text
conda update conda
```

3.Python创建虚拟环境

```text
conda create -n your_env_name python=x.x
```

anaconda命令创建python版本为x.x，名字为your_env_name的虚拟环境。**your_env_name文件可以在Anaconda安装目录envs文件下找到**。

4.激活或者切换虚拟环境

打开命令行，输入python --version检查当前 python 版本。

```text
conda activate bellma_env
```