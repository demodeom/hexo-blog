---
title: Win10 安装 mingw-w64
date: 2021-07-11 07:41:25
tags: 
    - C
    - Win
---

Win10 搭建 C 开发环境, 安装步骤非常简单, 但是下载地址不是那么容易找

<!-- more -->

## 下载

### 下载安装包

mingw-w64 官网地址是 [http://mingw-w64.org/doku.php](http://mingw-w64.org/doku.php)， 但是，安装包被托管在 SourceForge [https://sourceforge.net/projects/mingw-w64/files/mingw-w64/mingw-w64-release/](https://sourceforge.net/projects/mingw-w64/files/mingw-w64/mingw-w64-release/)


**不要直接下载源码**，滑动页面， 找到以下位置, 下载二进制文件

![mingw.png](http://blog.image.codedemo.vip/mingw.png)

选择合适的版本， 例如： `MinGW-W64 GCC-8.1.0 x86_64-win32-seh`

### 版本选择

版本及其他参数的含义参考文章 [MinGW-w64安装教程——著名C/C++编译器GCC的Windows版本](https://zhuanlan.zhihu.com/p/76613134)

为了防止原文丢失, 简单提取一下关键信息:

- Gcc 版本建议使用最新版即可
- 32 位系统推荐 `i686` , 64 位系统推荐 `x86_64`
- 编译程序在 window 下运行选择 `win32` , 编译程序在 Linux 下运行选择 `posix` ,
- 异常处理模型: dwarf 的性能要优于 sjlj , 但是不支持 64位 


## 安装

- Step 1: 解压下载的压缩包 `MinGW-W64 GCC-8.1.0 x86_64-win32-seh`

- Step 2: 复制 `mingw64` 到指定开心的位置, 比如: `D:\Program Files`

- Step 3: 添加环境变量, 如: `D:\Program Files\mingw64\bin`

- Step 4: 使用命令 `gcc -v` 检查是否安装成功, 无报错信息即为安装成功

## Hello World

`hello_world.c`

```c
#include <stdio.h>

int main(int argc, char const *argv[])
{
    printf("%s\n", "Hello World");
    return 0;
}
```

编译

```bash
gcc hello_world.c -o hello_world.exe
```

运行

```bash
./hello_world.exe
```






