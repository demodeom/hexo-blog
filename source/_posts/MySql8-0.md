---
title: MySql8.0
date: 2021-08-03 14:01:19
tags:
---

## 安装

```bash
sudo apt install mysql-server-8.0 mysql-client-8.0
```

修改配置 `sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf` , 允许外网连接

```language
bind-address            = 0.0.0.0
```

## 服务管理

重启服务 

```bash
sudo service mysql restart
```

启动服务

```bash
sudo service mysql start
```

停止服务

```bash
sudo service mysql stop
```

服务状态

```bash
sudo service mysql status
```


MySQL连接

连接 MySQL需要以下信息

- host: MySQL 服务IP ,  如:     192.168.60.120
- port: MySQL 服务端口 ,  如:     3306
- user: MySQL 账号 , 如:     demodeom
- password: MySQL 账号对应的密码, 如:     123456

```bash
mysql -h 192.168.60.120 -P 3306 -u demodeom -p
```

`-p` 表示输入密码 , 直接在终端输入密码是不安全的 , 建议通过交互的方式输入密码 , 当看到 `Enter password:` 提示时 ,  输入密码 ,  然后按 `回车键` ;   密码输入过程是不可见的 ,  也不会被终端历史记录到.

如果 host 为本机地址 , 可以省略 `-h` , 例如: `mysql -h 192.168.60.120 -P 3306 -u demodeom -p`

如果 port 为默认 `3306` , 可以省略 -P , 例如: `mysql -h 192.168.60.120 -u demodeom -p`

如果 user 和当前用户名一致 , 可以省略 -u, 例如: `mysql -h 192.168.60.120 -P 3306 -p`


退出 `\q` 或者 `QUIT`, 然后按 `回车键` 


**MySQL几乎所有SQL语句以 `;` 结尾, 需要按 `回车键` 来执行SQL语句**,  后面将不在赘述

参考文章 

- [3.1 Connecting to and Disconnecting from the Server](https://dev.mysql.com/doc/refman/8.0/en/connecting-disconnecting.html)

执行 SQL

- 一个查询通常由一个SQL语句和一个分号组成。(有一些例外情况，分号可以省略。前面提到的QUIT就是其中之一。我们稍后将讨论其他的情况）。)
- 当你发出一个查询时，mysql将其发送到服务器执行，并显示结果，然后打印另一个 `mysql>` 提示，表明它已准备好进行另一个查询。
- mysql 以表格的形式显示查询输出（行和列）。第一行包含各列的标签。后面的行是查询结果。通常情况下，列标签是你从数据库表中获取的列的名称。如果你检索的是表达式的值，而不是表的列（如刚才的例子），mysql会使用表达式本身来标注列。
- mysql 显示了返回的行数和查询的执行时间，这让你对服务器的性能有一个大致的了解。这些值是不精确的，因为它们代表挂钟时间（而不是CPU或机器时间），而且它们受到服务器负载和网络延迟等因素的影响。(为了简洁起见，在本章其余的例子中，有时不显示 "集合中的行 "这一行）。)

**MySQL关键字不区分大小写**

什么是关键字？ 如： `select` `where` `and` 等


Example

```
mysql> SELECT VERSION(), CURRENT_DATE;
+-------------------------+--------------+
| VERSION()               | CURRENT_DATE |
+-------------------------+--------------+
| 8.0.26-0ubuntu0.20.04.2 | 2021-08-03   |
+-------------------------+--------------+
1 row in set (0.00 sec)
```

VERSION() 内置函数，MySQL 有很多内置函数；
CURRENT_DATE 内置变量，MySQL 有很多内置变量；

除此之外， MySQL 还支持算术运算

```
mysql> SELECT SIN(PI()/4), (4+1)*5;
+------------------+---------+
| SIN(PI()/4)      | (4+1)*5 |
+------------------+---------+
| 0.70710678118655 |      25 |
+------------------+---------+
1 row in set (0.02 sec)
```

常见提示符

- `mysql>` 为新的查询做好准备
- `->` 等待多行查询的下一行
- `'>` 等待下一行，等待以单引号开始的字符串的完成(`'`)
- `">` 等待下一行，等待完成以双引号开始的字符串(`"`)
- `\`>` 等待下一行，等待完成以反斜线开头的标识符（`\``）
- `/*`> 等待下一行，等待以`/*`开头的注释的完成


参考文章 [3.2 Entering Queries](https://dev.mysql.com/doc/refman/8.0/en/entering-queries.html)

数据库

创建数据库

```sql
create database test;
```

查看数据库

```sql
show databases;
```

切换数据库

```sql
use test;
```

数据表

查看数据表

```sql
show tables;
```

创建数据表

```sql
create table admin_user(
    id int not null comment "序号",
    user_name varcahr(10) not null comment "用户名", 
    index(user_name)
)
```

创建数据表SQL语句由以下部分组成

- 表名
- 字段
- 索引(非必须)
- 表设置(非必须)

通过 `CREATE TABLE table_name()` 来创建表，
字段和索引包含在 `()` 中，字段与索引之间通过 `,` 分割，最后一个省略`,`

字段由以下几部分组成

- 字段名称 
- 数据类型 
- 约束 
- 注释等

常见的文本类型由  varchar char text 
常见的整数类型由 tinyint smallint int big int
常见的浮点类型 float demi




## 用户

### 创建用户

```sql
create user "demodeom"@"%" identified by "123456";
```

### 权限

分配权限

```sql
grant all privileges on *.* to "demodeom"@"%" with grant option;
```

### 更强的加密

使用 `desc user;` 可以看到默认为 caching_sha2_password

```sql
`plugin` char(64) COLLATE utf8_bin NOT NULL DEFAULT 'caching_sha2_password',
```

使用 `SELECT User, Host, plugin from user where User="root";` 可以查到

```
+------+-----------+-------------+
| User | Host      | plugin      |
+------+-----------+-------------+
| root | localhost | auth_socket |
+------+-----------+-------------+
```

这也是为何 root 用户无法远程登录的原因之一

