# ubuntu

安装mysql 

```shell
sudo apt-get install mysql-server
```

安装完成之后修改Mysql的安全配置

```shell
sudo mysql_secure_insatallation	
```



# centos

```shell
# 下载mysql源安装包
wget http://dev.mysql.com/get/mysql57-community-release-el7-8.noarch.rpm
# 安装mysql源
yum localinstall mysql57-community-release-el7-8.noarch.rpm
```

检查mysql源是否安装成功

```shell
yum repolist enabled | grep "mysql.*-community.*"
```

可以修改`vim /etc/yum.repos.d/mysql-community.repo`源，改变默认安装的mysql版本。比如要安装5.6版本，将5.7源的enabled=1改成enabled=0。然后再将5.6源的enabled=0改成enabled=1即可。改完之后的效果如下所示： 
![这里写图片描述](https://www.linuxidc.com/upload/2016_09/160918124758197.jpg)

安装Mysql

```shell
yum install mysql-community-server
```

启动mysql服务

```shell
systemctl start mysqld
```

查看mysql状态

```shell
systemctl status mysqld
```

开机启动

```shell
systemctl enable mysqld
systemctl daemon-reload
```

修改root密码

```shell
grep 'temporary password' /var/log/mysqld.log
```

![rooté»è®¤å¯ç ](https://www.linuxidc.com/upload/2016_09/160918124758193.png)

```mysql
set password for 'root'@'localhost'=password('password')
```

mysql5.7开始密码加入校验规则，必须包含大写字母、小学字母、特殊符号等。



### 允许mysql远程连接

默认安装完成的mysql是不支持远程连接的，所以需要修改配置使其支持远程连接。

```shell
sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
```

找到`bind-address = 127.0.0.1`并注释掉这段或者把地址改为0.0.0.0。

`bing-address = 127.0.0.1`表示的是只有本地能远程连接，0.0.0.0表示任何人都可以。



### 重启mysql

修改完配置文件之后需要使配置文件生效

```shell
sudo service mysql restart

systemctl restart mysqld
```



### 登录mysql 

```shell
sudo mysql -u root -p
```

### 添加新用户

```mysql
grant all privileges on *.* to 'root'@'%' identified by 'root的密码'
```

