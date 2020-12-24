# 安装docker

从官网获取docker安装脚本

```shell
curl -fsSL get.docker.com -o get-docker.sh 
```

设置数据源为阿里云

```shell
sh get-docker.sh --mirror Aliyun
```

查看是否修改成功

```shell
docker version
```

设置开机启动

```shell
systemctl enbale docker
```

检查是不是开机启动

```shell
systemctl is-enabled docker
```



# 修改docker 文件存储引擎

在一台宿主机上只能选择一种docker文件存储引擎，修改完成后需要重启docker，修改完成后现有容器和镜像不可用。

修改配置文件`/etc/docker/daemon.json`,没有则新建,

```json
{
  "storage-driver": "overlay2" 
}
```



# 设置镜像加速

修改配置文件`/etc/docker/`下的daemon.json文件,没有则新建

在文件里面加上下面的json数据

```json
{
  "registry-mirrors": [
    "http://hub-mirror.c.163.com"
  ]
}
```



然后重新加载文件

```shell
sudo systemctl daemon-reload
```

重启docker

```shell
sudo systemctl restart docker
```



