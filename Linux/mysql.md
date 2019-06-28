安装mysql 

```shell
sudo apt-get install mysql-server
```

安装完成之后修改Mysql的安全配置

```shell
sudo mysql_secure_insatallation	
```



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
```

### 登录mysql 

```shell
sudo mysql -u root -p
```

### 修改root用户权限

授权root用户，改为允许所有人连接

```mysql
grant all privileges on *.* to 'root'@'%' identified by 'root的密码'
```

