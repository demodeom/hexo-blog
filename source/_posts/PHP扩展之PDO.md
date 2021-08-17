---
title: PHP扩展之PDO
date: 2021-08-15 20:41:06
tags:
    - PDO
categories:
    - PHP
---

PHP扩展之PDO

<!-- more -->


## 支持的驱动


## 常用的函数

### 事务

#### beginTransaction

**说明**

`PDO::beginTransaction(): bool`

关闭自动提交模式。自动提交模式被关闭的同时，通过 PDO 对象实例对数据库做出的更改直到调用 PDO::commit() 结束事务才被提交。调用 PDO::rollBack() 将回滚对数据库做出的更改并将数据库连接返回到自动提交模式。

包括 MySQL 在内的一些数据库，当发出一条类似 DROP TABLE 或 CREATE TABLE 这样的 DDL 语句时，会自动进行一个隐式地事务提交。隐式地提交将阻止你在此事务范围内回滚任何其他更改。

**返回值**


成功时返回 true， 或者在失败时返回 false。

#### commit

**说明**

`PDO::commit(): bool`

提交一个事务，数据库连接返回到自动提交模式直到下次调用 PDO::beginTransaction() 开始一个新的事务为止。

**返回值**

成功时返回 true， 或者在失败时返回 false。

#### rollBack

**说明**

`PDO::rollBack(): bool`

回滚由 PDO::beginTransaction() 发起的当前事务。如果没有事务激活，将抛出一个 PDOException 异常。

如果数据库被设置成自动提交模式，此函数（方法）在回滚事务之后将恢复自动提交模式。

包括 MySQL 在内的一些数据库， 当在一个事务内有类似删除或创建数据表等 DLL 语句时，会自动导致一个隐式地提交。隐式地提交将无法回滚此事务范围内的任何更改。

**返回值**

成功时返回 true， 或者在失败时返回 false。

### 数据库连接

#### construct

**说明**

`PDO::__construct( string $dsn, string $username = ?, string $password = ?, array $driver_options = ?)`

创建一个表示连接到请求数据库的数据库连接 PDO 实例。

**参数**

- dsn 数据源名称或叫做 DSN，包含了请求连接到数据库的信息。
- username DSN字符串中的用户名。对于某些PDO驱动，此参数为可选项。
- password DSN字符串中的密码。对于某些PDO驱动，此参数为可选项。
- driver_options 一个具体驱动的连接选项的键=>值数组。

关于 dsn 和  driver_options 配置可以在 [PDO驱动](https://www.php.net/manual/zh/pdo.drivers.php) 查找

**返回值**

成功则返回一个PDO对象。

**错误／异常**

如果试图连接到请求的数据库失败，则`PDO::__construct()` 抛出一个 PDO异常（PDOException） 。

**Example**

```php
$dsn = "mysql:dbname=test;port=3306;host=192.168.10.10";
$user_name = "demodeom";
$user_password = "123456";

try{
    $dbh = new PDO($dsn, $user_name, $user_password);
}catch(PDOException $e){
    echo "connect failed " . $e->getMessage();
}
```

### 执行 SQL 语句

#### exec


**说明**

PDO::exec(string $statement): int

PDO::exec() 在一个单独的函数调用中执行一条 SQL 语句，返回受此语句影响的行数。

PDO::exec() 不会从一条 SELECT 语句中返回结果。对于在程序中只需要发出一次的 SELECT 语句，可以考虑使用 PDO::query()。对于需要发出多次的语句，可用 PDO::prepare() 来准备一个 PDOStatement 对象并用 PDOStatement::execute() 发出语句。

**参数**

statement 要被预处理和执行的 SQL 语句。

**返回值**

PDO::exec() 返回受修改或删除 SQL 语句影响的行数。如果没有受影响的行，则 PDO::exec() 返回 0。 

#### query

**说明**

`public PDO::query(string $statement): PDOStatement`
`public PDO::query(string $statement, int $PDO::FETCH_COLUMN, int $colno): PDOStatement`
`public PDO::query(string $statement,int $PDO::FETCH_CLASS,string $classname,array $ctorargs): PDOStatement`
`public PDO::query(string $statement, int $PDO::FETCH_INTO, object $object): PDOStatement`

PDO::query() 在单次函数调用内执行 SQL 语句，以 PDOStatement 对象形式返回结果集（如果有数据的话）。

如果反复调用同一个查询，用 PDO::prepare() 准备 PDOStatement 对象，并用 PDOStatement::execute() 执行语句，将具有更好的性能。

如果没有完整获取结果集内的数据，就调用下一个 PDO::query()，将可能调用失败。 应当在执行下一个 PDO::query() 前，先用 PDOStatement::closeCursor() 释放数据库PDOStatement 关联的资源。


如果传入函数的参数数量超过一个，多余的参数将相当于调用结果对象 PDOStatement::setFetchMode() 方法。

**参数**

statement 需要准备、执行的 SQL 语句。

**返回值**

PDO::query() 返回 PDOStatement 对象，或在失败时返回 false。

