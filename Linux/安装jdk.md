### 上传JDK压缩包

```shell
sudo rz 
```

回车后打开选择文件上传界面

### 安装

1. 在本地/usr/local目录下新建个java目录

   ```shell
   sudo mkdir -p /usr/local/java 
   ```

2. 解压上传的jdk压缩包

   ```shell
   sudo tar -zxvf fileName
   ```

3. 移动解压后的文件夹到/usr/local/java下

   ```shell
   sudo mv fileName /user/local/java
   ```



### 配置环境变量

配置系统环境变量,路径`/ect`

```shell
sudo vi /ect/environment
```

添加环境变量

```shell
export JAVA_HOME=/usr/local/java/jdkFileName
export JRE_HOME=/usr/local/java/jdkFileName/jre
export CLASSPATH=$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib
```

配置用户环境变量

```shell
sudo vi /ect/profile
```

在保证其他语句完整的情况下找空白处添加环境变量的语句

```shell
export JAVA_HOME=/usr/local/java/jdkFileName
export JRE_HOME=/usr/local/java/jdkFileName/jre
export CLASSPATH=$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib
```

### 使环境变量生效

上面一系列配置完成之后还需要使环境变量生效或重启系统

```shell
source /ect/profile
```



