https://blog.csdn.net/weixin_43837968/article/details/114776842

Anaconda/conda详解

一、介绍

开源包管理系统和环境管理系统 ，包括多种语言的包安装，运行，更新，删除，最重要的是可以解决包依赖问题
支持语言包括 Python，R，Ruby，Lua，Scala，Java，JavaScript，C / C ++，FORTRAN
支持在Windows，macOS和Linux上运行
Conda可以构建不同的环境，同时可以对环境进行保存，加载和切换操作
conda包和环境管理器包含在所有版本的Anaconda和Miniconda中
二、安装

1.下载

下载地址1：官方下载
下载地址2：清华大学开源软件镜像站

2.安装

参见linux和Windows下软件安装说明

3.配置环境

查看是否安装成功,如果安装没问题会显示conda版本号
conda [--version | -V]
新建环境, your_env_name是环境名称，对环境的操作后面会详述
conda create [--name | -n] your_env_name
激活环境
conda activat

三、使用及命令详解

1. 包管理功能

搜索包

conda search fastqc

安装包

安装特定包
conda install fastqc
安装特定版本的软件包（查看软件版本可以使用）
conda search fastqc
conda install fastqc=0.11.6
安装多个包
conda install fastqc multiqc

包更新

更新特定包
conda update fastqc
更新Python
conda update python
更新conda本身及Anaconda元数据包
conda update conda
conda update anaconda
防止包更新
conda update fastqc --no-pin

包删除

删除当前环境中的包
conda remove pkg_name
删除特定环境中的包
conda remove -n env_name pkg_name
删除多个包
conda remove pkg_name1 pkg_name2

包列表

列出当前环境所有包
conda list
列出特定环境所有包
conda list -n env_name
列出已安装包的版本
conda list pkg_name

2. 环境管理功能

每个环境都有自己独立的软件或开发包列表，并会自动添加相应的环境变量和依赖关系。

创建环境

创建特定名字的环境
conda create -n env_name
使用特定版本的Python创建环境
conda create -n env_name python=3.4
使用特定包创建环境
conda create -n env_name pandas
用 environment.yml 配置文件创建环境
conda env create -f environment.yml

environment.yml 文件:

name: stats2 channels: - javascript dependencies: - python=3.4 # or 2.7 - bokeh=0.9.2 - numpy=1.9.* - nodejs=0.10.* - flask - pip: - Flask-Testing
激活环境

conda activate env_name

查看环境（当前环境用 * 表示）

命令1：
conda env list
命令2：
conda info [--envs | -e]

停用环境

conda deactivate env_name

删除环境

conda remove -n env_name
若无法删除，可使用命令
conda remove -n env_name --all

构建相同的conda环境（不同机器间的环境复制）

激活需要导出配置文件的环境
conda list --explicit > files.txt
在同系统的不同机器执行
conda create [--name | -n] env_name -f files.txt

克隆环境（同一台机器的环境复制)

conda create --name clone_env_name --clone env_name

导出环境文件

导出environment.yml环境文件，需要先激活，再导出
激活需要导出文件的环境
conda activate env_name
导出
conda env_name export > environment.yml

3. 渠道管理

这个决定你从哪个站点，下载及安装资源包

添加新渠道到顶部，最高优先级
conda config --add channels new_channel
或者
conda config --prepend channels new_channel

添加新渠道到底部，最低优先级
conda config --append channels new_channel

4. 创建不同版本的Python环境

Python 3.6 的 Anaconda 环境
conda create -n py36 python=3.6 anaconda
Python 2.7 的 Anaconda 环境
conda create -n py27 python=2.7 anaconda

5. 动态链接库解决方案

错误: error while loading shared libraries
问题在于无法链接动态库（Windows下是.dll文件, Linux下是.so文件）

解决办法:
将动态链接库文件目录添加至环境变量或者添加至工作目录