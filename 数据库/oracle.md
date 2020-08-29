# 基本命令

##  lsnrctl 监听命令

`lsnrctl status`：查看当前监听器状态。

`lsnrctl stop [listener-name]`：停止所有监听器，可以停止指定名称的监听器。

`lsnrctl start [listener-name]`：启动所有监听器，可以启动指定名称的监听器。

`lsnrctl reload`：重启监听器。

# 数据库相关操作

### 查看用户拥有的表

```sql
-- 查看用户拥有的表
select table_name from user_tables
```

### 查询当前系统时间

```sql
-- 查询当前系统时间
select to_char(sysdate,'yyyy-MM-dd hh[24]:mi:ss') from dual;
```

### 修改当前会话时间格式

```sql
-- 修改当前会话时间格式
alter session set nls_date_format='yyyy-MM-dd hh[24]:mi:ss';
```

### 复制表

```sql
-- 复制表
create table target_table as select * from src_table [where expression];

-- 复制表数据 
insert into target_table select * from src_table [where expression]
```



## 函数

###  to_date

to_date('date_time',format)：将时间转换为指定格式。

### initcap(str)

initcap(str)：将字符串首字母大写。

```sql
select initcap('abc') from dual;   -- 结果为Abc
```

### upper

### lower

### substr

### replace

