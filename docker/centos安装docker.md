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



