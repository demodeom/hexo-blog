---
title: C - 笔记
date: 2021-08-12 07:14:23
tags:
    - 笔记
categories:
    - C
---

C - 笔记

<!-- more -->


## C 标准

### C89 或者 C90

1. 美国国家标准协会（ANSI）于 1983 年组建了一个委员会（X3J11），开发了一套新标准，并于1989年正式公布
1. 国际标准化组织于1990年采用了这套C标准（ISO C）
1. ISO C和ANSI C是完全相同的标准。ANSI/

ISO标准的最终版本通常叫作C89或C90

### C99

1994年，ANSI/ISO联合委员会（C9X委员会）开始修订C标准，最终发布了C99标准


### C11

标准委员会在2007年承诺C标准的下一个版本是C1X，2011年终于发布了C11标准

## 使用C语言的7个步骤

1. 定义程序的目标
1. 设计程序
1. 编写代码
1. 编译
1. 运行程序
1. 测试和调试程序
1. 维护和修改代码

## HelloWorld


```c
#include <stdio.h>

int main() {
    printf("Hello, World!\n");
    return 0;
}
```

## 基础语法

### 定义变量

变量必须先定义, 后使用

```c
int num;
num = 18;
```

### 语句结束符

语句以 `;` 结束

```c
int num; num = 18;
```

语句之间的换行或者空格是非必需的, 但是 `;` 是必须的

### 注释

单行注释

```c
// 单行注释
```

多行注释

```c
/*

    多行
    注释
*/
```

### 标识符

C99和C11允许使用更长的标识符名，但是编译器只识别前63个字符。对于外部标识符（参阅第12章），只允许使用31个字符

实际上，你可以使用更长的字符，但是编译器会忽略超出的字符(这样是很危险的，标识符可能冲突)

可以用小写字母、大写字母、数字和下划线（\_）来命名。而且，名称的第1个字符必须是字符或下划线，不能是数字

C语言的名称区分大小写

什么是标识符 ？？？ 比如： 常量名称，变量名称，结构体名称

## 语句

### 声明变量

```c
int num;
```
多条声明(前提： 变量类型相同)

```bash
int feet, fathoms;
```

### 赋值表达式

```c
num = 18;
```

### 函数调用

```c
printf("Hello, World!\n");
```

### return

```c
return 0;
```


## printf

printf() 发送格式化输出到标准输出, 被包含在 `stdio.h` 标准库

打印字符串

```c
#include <stdio.h>

int main(int argc, char const *argv[])
{
    printf("hello world");
    return 0;
}
```

打印一个变量

```c
#include <stdio.h>

int main(int argc, char const *argv[])
{
    char name[] = "Tony";
    printf("%s\n", name);
    return 0;
}
```

打印多个变量

```c
#include <stdio.h>

int main(int argc, char const *argv[])
{
    char name[] = "Tony";
    int age = 18;
    printf("My name is %s, my age is %d;\n", name, age);
    return 0;
}
```

- `%d` 以十进制形式输出带符号整数(正数不输出符号)
- `%o` 以八进制形式输出无符号整数(不输出前缀0)
- `%x` 以十六进制形式输出无符号整数(不输出前缀Ox)
- `%u` 以十进制形式输出无符号整数
- `%f` 以小数形式输出单、双精度实数
- `%e` `%E` 以指数形式输出单、双精度实数
- `%c` 输出单个字符
- `%s` 输出字符串
- `%p` 输出指针地址
- `%lu` 32位无符号整数
- `%llu` 64位无符号整数
