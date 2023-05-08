

# Python项目

1. 从代码仓库获取项目源码

   `git clone {代码仓库地址}`

2. 对于存在 submodule 的项目，获取 submodule 相关源码

   `cd` `{项目所在目录}`

   ``git submodule update --init  ``# 首次执行需要 --init 参数`

3. 创建项目虚拟环境

   `mkvirtualenv {虚拟环境名称}`

   ***mkvirtualenv dos_env -p ~/.pyenv/versions/2.7.18/bin/python***

   

4. 在虚拟环境中安装项目 requirements.txt 文件中所列出的依赖包

   ```shell
   workon {虚拟环境名称}
   cd {项目所在目录}
   pip install -r requirements.txt
   deactivate 退出虚拟环境
   ```

5. 修改本地配置文件

6. 尝试本地启动相关项目，执行 `**python runserver.py**`*` `*或按照项目 README.md 文件中指示执行

   

# 开发工具相关

## pyenv

- macOS / Ubuntu

  1. 获取pyenv源码到本地 ~/.pyenv 目录下

     ```shell
     git clone https://github.com/pyenv/pyenv.git ~/.pyenv
     ```

     

  2. 在个人profile中添加如下内容

     ```shell
     export PYENV_ROOT="$HOME/.pyenv"
     export PATH="$PYENV_ROOT/bin:$PATH"
     eval "$(pyenv init -)"
     ```

- Windows

- - 略

## virtualenv && virtualenvwrapper

- macOS / Ubuntu

  1. 通过pip在全局环境安装 virtualenv 和 virtualenvwrapper

     `sudo` `pip ``install` `virtualenv``sudo` `pip ``install` `virtualenvwrapper`

  2. 在个人profile中添加如下内容

     `source` `/usr/local/bin/virtualenvwrapper``.sh`

- Windows

- - 略

## arcanist

- macOS / Ubuntu

  1. 获取 arcanist 和 libphutil 到本地 ~/.arcanist 目录下

     `mkdir` `~/.arcanist``cd` `~/.arcanist``# git clone https://github.com/phacility/arcanist.git``# git clone https://github.com/phacility/libphutil.git``# 由于目前官方版本在使用上有些问题，我们使用 @weizhibo FIX 的一个版本``git clone git@git.corp.imdada.cn:weizhibo``/arcanist``.git``git clone git@git.corp.imdada.cn:weizhibo``/libphutil``.git`

  2. 在个人profile中添加如下内容

     `export` `PATH=``"$HOME/.arcanist/arcanist/bin:$PATH"`

  3. 执行 arc install-certificate [uri] 命令

     `arc ``install``-certificate http:``//phabricator``.corp.imdada.cn/`

  4. 访问 http://phabricator.corp.imdada.cn/conduit/login/ 获取 API Token，输入到命令行中

      

  5. 可以执行如下命令配置默认编辑器为vim

     `arc ``set``-config editor ``"vim"`

  6. 可以在 ~/.arcrc 文件中查看当前相关配置

     `{`` ``"hosts"``: {``  ``"http://phabricator.corp.imdada.cn/api/"``: {``   ``"token"``: ``"this-is-token"``  ``}`` ``},`` ``"config"``: {``  ``"editor"``: ``"vim"`` ``}``}`

- Windows

  - 略



