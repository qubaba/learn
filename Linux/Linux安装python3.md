# 下载安装包

[下载安装包](下载安装包,https://www.python.org/downloads/release/python-368/)

![下载如下](C:\Users\qp\Desktop\md图片\linux-python-安装包下载.png)

# 安装python依赖插件

python安装之前需要一些必要的模块，比如openssl，readline等，如果没有这些模块后来使用会出现一些问题，比如没有openssl则不支持ssl相关的功能，并且pip3在安装模块的时候会直接报错；没有readline则python交互式界面删除键和方向键都无法正常使用，至于需要什么模块在make完之后python会给出提示，通过提示进行安装即可装全， 另外感谢园友的Glory_Lion的回复；下面是需要提前预装的依赖：

```shell
yum -y install zlib zlib-devel
yum -y install bzip2 bzip2-devel
yum -y install ncurses ncurses-devel
yum -y install readline readline-devel
yum -y install openssl openssl-devel
yum -y install openssl-static
yum -y install xz lzma xz-devel
yum -y install sqlite sqlite-devel
yum -y install gdbm gdbm-devel
yum -y install tk tk-devel
yum -y install libffi libffi-devel
```

# 解压python源码

```shell
tar -xvzf Python-3.5.1.tgz
```

# 进入源码目录

```shell
cd Python-3.5.1/
```

# 配置编译

```shell
./configure --prefix=/usr/python --enable-shared CFLAGS=-fPIC
```

# 编译源码

```shell
make
```

# 执行安装

```shell
make install
```

# 修改软链

不修改原来的python配置的情况下配置python3，执行代码的时候吧python改为python3即可。pip也改为pip3p

```shell
ln -s /usr/python/bin/python3 /usr/bin/python3
ln -s /usr/python/bin/pip3 /usr/bin/pip3
```

