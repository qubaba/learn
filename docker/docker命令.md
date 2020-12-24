# docker 镜像命令

**列出镜像:** `docker images`

**列出所有镜像:** `docker images -a`

虚悬镜像:镜像名和标签都为none，这种镜像原本是有镜像名和标签的但是随着官网的更新重新 `docker pull 镜像名` 的时候会把原来的镜像名和标签转移到新下载的镜像上，原来镜像的镜像名和标签置为none并且失去意义

**列出所有虚悬镜像:**`docker image ls -f dangling=true`

**删除虚悬镜像:** `docker image prune`

**列出部分镜像:** `docker images image:tag`

**列出镜像名之后建立的镜像:**`docker images  -f/--filter since=image`

**列出镜像名之前建立的镜像:**`docker images  -f/--filter before=image`

**删除镜像:** `docker image rm imageName`

**查看镜像:**`docker ps [-al]`

**镜像导出命令:**`docekr image save image:tag -o file`

**镜像加载命令:** `docker image load -i file`

**通过dockerfile构建镜像：**`docker build -t dockerImageName:version dockerfilePath`

# docker 容器命令

容器管理命令:

`docker run [-d]  [-p] [-it]  [-v] --name image /bin/bash`  

`run`运行docker容器

`-d`指定容器后台运行

`-p 宿主机端口号:docker容器端口号` 绑定端口号

`-v宿主机目录:docker目录` 绑定目录卷

`-it` 分配一个交互式终端 

`/bin/bash`覆盖容器的初始命令

  

**删除容器:** `docker rm 容器ID`

**启动Docker**:`docker run -it [--rm] 容器名`

**进入容器:**`docker exec -it 容器Id bash`



# docker 网络命令



# dockerfile命令

`FROM`指定依赖环境

`RUN`需要运行的命令

`CMD`容器启动的命令

`ADD` 把宿主机的某某个文件copy到docker容器