# 配置文件

## lower_case_table_names

lower_case_table_names：表名小写，取值0、1、2。

0：表和数据库名存储在磁盘上。名称比较是区分大小写的。

1：名称比较不区分大小写。MySQL将所有表名转换为小写的存储和查找。这种行为也适用于数据库名称和表别名。

2：表和数据库名称存储在磁盘上，使用CREATE Table中指定的lettercase或创建数据库语句，

但是MYSQL将它们转换为小写的查找。名称比较不区分大小写。这只适用于不区分大小写的文件系统!InnoDB表名和视图名以小写形式存储，至于小写的case_table_name =1

## skip-name-resolve

skip-name-resolve：禁用dns解析

## skip_external_locking

skip_external_locking：外部锁定，当多台主机同时访问一台数据库服务器时，只有当前任务完成事务提交并且解除当前锁定时，下次任务才能执行。

##  character-set-server

character-set-server：服务编码 取值utf8

## default-storage-engine

default-storage-engine：默认存储引擎，mysql引擎MYISAM、InnoDB。

## server-id

server-id：服务id，

1. mysql的同步的数据中是包含server-id的，用于标识该语句最初是从哪个server写入的，所以server-id一定要有的
2.  每一个同步中的slave在master上都对应一个master线程，该线程就是通过slave的server-id来标识的；每个slave在master端最多有一个master线程，如果两个slave的server-id 相同，则后一个连接成功时，前一个将被踢掉。 这里至少有这么一种考虑： slave主动连接master之后，如果slave上面执行了slave stop；则连接断开，但是master上对应的线程并没有退出；当slave start之后，master不能再创建一个线程而保留原来的线程，那样同步就可能有问题；
3. 在mysql做主主同步时，多个主需要构成一个环状，但是同步的时候有要保证一条数据不会陷入死循环，这里就是靠server-id来实现的



# 导入/导出sql

```mysql
--- 导出
mysqldump --no-defaults -h host -uadmin -padmin --default-character-set=utf8 dbName > path

mysql -h host -uadmin -padmin -DdbName < path
```

# 添加用户并授权

```mysql
--- host %代表是所以地址都可以访问
create user 'userName'@'host' identified by 'password'; 

--- 授权用户 dbName为*时授权所有数据库，table为*时授权所有表
grant all privileges on dbName.table to 'userName'@'host' identified by 'password' with grant option;

--- 刷新权限
flush privileges;
```

# sql

## 删除数据库

```mysql
drop database if exists dbName
```

## 建库语句

```mysql
create database dbName CHARACTER SET utf8 COLLATE utf8_general_ci;
```

## 修改数据库字符集

```mysql
alter database dbName CHARACTER SET utf8 COLLATE utf8_general_ci;
```

## 查看所有库编码

```mysql
show character set 
```

