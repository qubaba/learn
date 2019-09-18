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
-- host %代表是所以地址都可以访问
create user 'userName'@'host' identified by 'password'; 

-- 授权用户 dbName为*时授权所有数据库，table为*时授权所有表
grant all privileges on dbName.table to 'userName'@'host' identified by 'password' with grant option;

-- 刷新权限
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
show character set;
```

```mysql
-- 查看当前库编码集
show variables like 'character_set_database';
```

## 分析Sql执行

```mysql
explain select * from table
```

## 查看支持的引擎

```mysql
show engines;
```

## 查看当前默认引擎

```mysql
show variables like "%storage_engine%";
```

## 查看Mysql版本

```mysql
select version();
```

## 表修改语句

```mysql
-- 修改字段数据类型
alter table <表名>  modify <字段名> <数据类型>

-- 添加列
alter table <表名>  add column <字段名> <数据类型>  |[first] 表示添加字段到首行|[after <字段名>] 添加字段到某列之前|

-- 删除列
alter table <表名>  drop <字段名>;

-- 修改字段名称
ALTER TABLE <表名> CHANGE <旧字段名> <新字段名> <新数据类型>；
-- 修改表名
ALTER TABLE <旧表名> RENAME [TO] <新表名>；


```

## 删除数据库表

```mysql
drop table if exists  <表名> [ , <表名1> , <表名2>] 
```

## 主键

```mysql
-- 创建表 定义列的时候就设置好主键。
<字段名> <数据类型> PRIMARY KEY [默认值]

CREATE TABLE tb_emp3
    (
    id INT(11) PRIMARY KEY,
    name VARCHAR(25),
    deptId INT(11),
    salary FLOAT
    );
-- 建表语句之后添加主键 
primary key(<列名>)

CREATE TABLE tb_emp4
   (
   id INT(11),
   name VARCHAR(25),
   deptId INT(11),
   salary FLOAT,
   PRIMARY KEY(id)
   );
-- 添加复合主键
CREATE TABLE tb_emp5
   (
   name VARCHAR(25),
   deptId INT(11),
   salary FLOAT,
   PRIMARY KEY(id,deptId)
   );
-- 修改列时添加主键
alter table <表名> add primary key(<列名>)

-- 删除主键
alter table <表明> drop primary key;

```



## 外键

主表定义：对于两个具有关联关系的表而言，相关联字段中主键所在的表即为主表。

从表定义：对于两个具有关联关系的表而言，相关联字段中外键所在的表即为从表。

### 外键定义规则

* 定义外键时主表必须在数据库中已经存在，或者是当前已经在创建的表，如果是当前在创建的表，这种情况可以成为自参照表，这种结构成为自参照完整性。
* 必须为主表定义主键。
* 主键不能包含空值，但是外键可以包含空值。
* 外键的数据类型必须和主表中对应列的数据类型一致。
* 外键中列的数目必须和主表中对应列的数目相同。
* 在主表的表名后面指定列名或列名的组合。这个列或列的组合必须是主表的主键或候选键。 主要指的是references <主表>(<列名>)。

```mysql

-- 建表时添加外键

create table if not exists user_info(
	id int(11) primary key,
    phone int(11) not null,
    user_id int(11),
    constraint fk_user_id
    foreign key(user_id) references user(id)
);

create table if not exists <表名>(
	<列名> int(11) primary key,
    <列名> int(11) not null,
    <外键列名> int(11),
    constraint <外键名>
    foreign key(<外键列名>) references <主表名>(<主表列>[,<主表列2>...])
);


-- 修改表时添加外键
alter table <表名> 
	add constraint <外键名> 
	foreign key(<外键列名>) 
	references <主表名>(<主表列>[,<主表列2>...])
	
	
-- 删除外键约束
alter table <表名> drop foreign key <外键名>

```



## 唯一约束

唯一约束和主键的区别是一个表中只能有一个主键，但是可以存在多个唯一约束。声明为主键的列不能存在空值，但是唯一约束的列可以存在空值。



```mysql
-- 建表时添加唯一约束

create table if not exists <表名>(

    <列名> int primary key,
    <列名> int unique, -- unique 为唯一约束。
);

-- 修改列时添加唯一约束
alter table <表名> add constraint <唯一约束名称> unique(<列明>);

-- 删除唯一约束
alter table <表名> drop index <唯一约束名>
```





# mysql事务

## 事务隔离级别

1. 读未提交： 读到其他会话未提交的事务修改的数据
2. 读已提交：只能读到已提交事务的数据
3. 可重复读：同一事务内，查询结果都是事务刚开始的状态。
4. 序列化