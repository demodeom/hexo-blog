---
title: Ubuntu 搭建开发环境-Java
date: 2021-11-24 10:26:46
tags:
    - Java
    - 开发环境
categories:
    - Ubuntu
---

Java 开发环境搭建

<!-- more -->

## 下载

[Java Downloads](https://www.oracle.com/java/technologies/downloads/)

下载需要登陆 Oracle 账号，

阿里云盘下载地址 [jdk-11.0.13_linux-x64_bin.deb](https://www.aliyundrive.com/s/3dxHQMM5qah)


## 配置

```
export JAVA_HOME=/usr/lib/jvm/jdk-11
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export PATH=$JAVA_HOME/bin:$PATH
```