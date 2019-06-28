ubuntu使用Xshll如果连接不上的话需要检查SSH服务是否安装

### 检查软件是否安装

```shell
apt-cache policy openssh-server openssh-client
```

#### 安装SSH服务端

``` shell
sudo apt-get install openssh-server
```

#### 安装SSH客户端

``` shell
sudo apt-get install openssh-client
```

### 设置root用户密码

ubuntu root用户默认不开启 如果需要开启需要自己设置密码

```shell
sudo passwd root
```

# 设置root用户可以远程连接

```shell
sudo vi /etc/ssh/sshd_config
```

注释掉`PermitRootLogin without-password`加入`PermitRootLogin yes`



# 更换数据源

```shell
vi /etc/apt/sources.list
```

```shell
deb http://mirrors.aliyun.com/ubuntu/ xenial main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse
```

