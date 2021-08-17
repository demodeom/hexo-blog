---
title: Redis - 安装
date: 2021-08-12 06:43:47
tags:
    - Redis
    - Install
    - Ubuntu
categories:
    - Redis
---

介绍 Redis 版本选择、历史版本下载地址以及版本更新日志

<!-- more -->

## 版本

Redis [最新稳定版、版本历史、版本变更记录](https://hub.fastgit.org/redis/redis/releases)


## 安装

### 依赖

Ubuntu

```bash
sudo apt install -y unzip make gcc build-essential pkg-config tcl
```

### 下载源码

```bash
wget https://hub.fastgit.org/redis/redis/archive/refs/tags/6.2.5.zip
unzip 6.2.5.zip
```

### 编译

```bash
cd redis-6.2.5
make
```

看到以下错误，可能是之前编译失败缓存导致的，解决方法

1. 删除源码，重新解压
2. 编译

```
cd src && make all
make[1]: Entering directory '/home/us/redis-6.2.5/src'
    CC adlist.o
In file included from adlist.c:34:
zmalloc.h:50:10: fatal error: jemalloc/jemalloc.h: No such file or directory   50 | #include <jemalloc/jemalloc.h>
      |          ^~~~~~~~~~~~~~~~~~~~~
compilation terminated.
make[1]: *** [Makefile:368: adlist.o] Error 1
make[1]: Leaving directory '/home/us/redis-6.2.5/src'
make: *** [Makefile:6: all] Error 2
```


### 编译测试

```bash
make test
```

看到以下信息， 恭喜你通过编译测试

```
\o/ All tests passed without errors!
```

### 安装

安装目录： `/usr/local/bin`

```
sudo make install
```

源码目录提供了配置文件模板， 包括 `redis.conf` `sentinel.conf`

redis.conf 是 redis-server 的配置文件

sentinel.conf 是 redis-sentinel 的配置文件

### 配置文件管理

配置文件需要自己维护管理

```bash
sudo mkdir /etc/redis -p
sudo cp redis.conf  sentinel.conf  /etc/redis
```

## 服务维护

启动服务

```bash
redis-server /etc/redis/redis.conf
```

停止服务

```bash
# 找到进程ID
ps -aux | grep redis-server | grep -v grep | awk '{print $2}'
# 终止进程， 如： 75465
kill -15 75465
```
