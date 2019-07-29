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
    "https://registry.docker-cn.com"
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



# 命令

**列出镜像:** `docker images`

**列出所有镜像:** `docker images -a`

虚悬镜像:镜像名和标签都为none，这种镜像原本是有镜像名和标签的但是随着官网的更新重新 `docker pull 镜像名` 的时候会把原来的镜像名和标签转移到新下载的镜像上，原来镜像的镜像名和标签置为none并且失去意义

**列出所有虚悬镜像:**`docker image ls -f dangling=true`

**删除虚悬镜像:** `docker image prune`

**列出部分镜像:** `docker images 镜像名:[标签名]`

**列出镜像名之后建立的镜像:**`docker images  -f/--filter since=镜像名`

**列出镜像名之前建立的镜像:**`docker images  -f/--filter before=镜像名`

**删除镜像:** `docker image rm 镜像名`

**删除容器:** `docker rm 容器ID`

**查看镜像:**`docker ps [-al]`

**启动Docker**:`docker run -it [--rm] 容器名`

**进入容器:**`docker exec -it 容器Id bash`

**查看系统内核:**`cat /etc/issue`

