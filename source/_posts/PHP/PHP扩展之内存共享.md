---
title: PHP扩展之共享内存
date: 2021-08-07 04:59:31
tags:
    - 共享内存
categories:
    - PHP
---


PHP操作共享内存

<!-- more -->

## 定义

> 共享内存是 Unix下的多进程之间的通信方法 ,这种方法通常用于一个程序的多进程间通信，实际上多个程序间也可以通过共享内存来传递信息。

## 安装

使用 ` --enable-sysvshm` 编译 PHP

检查 `sysvsem` 是否安装

```bash
➜  ~ php -m | grep sysvshm
sysvshm
```

## 主要函数

- shm_attach - 创建或打开一个共享内存段
- shm_detach - 与共享内存段断开连接
- shm_get_var - 从共享内存中返回一个变量
- shm_has_var - 检查一个特定条目是否存在
- shm_put_var - 在共享内存中插入或更新一个变量
- shm_remove_var - 从共享内存中删除一个变量
- shm_remove - 从Unix系统中删除共享内存

### shm_attach

**语法:**

`shm_attach(int $key, ?int $size = null, int $permissions = 0666): SysvSharedMemory|false`


**功能:** 

shm_attach()返回一个id，这个id可以用来访问System V共享内存的给定键，第一次调用创建共享内存段的大小和可选perm-bits权限。

对同一个键第二次调用shm_attach()将返回一个不同的SysvSharedMemory实例，但两个实例都访问相同的底层共享内存，大小和权限将被忽略。

**参数:** 

- key 数字共享内存段 ID
- size 内存大小。如果没有提供，默认为php.ini中的sysvshm.init_mem，否则为10000字节。
- permissions 可选的权限位。默认为 0666。

**返回值:** 

成功时返回一个SysvSharedMemory实例，或者在失败时返回false。

### shm_detach

**语法:**

`shm_detach(SysvSharedMemory $shm): bool`

**功能:** 

shm_detach()断开与由shm_attach()创建的shm给出的共享内存的连接。记住，共享内存仍然存在于Unix系统中，数据仍然存在。

**参数:** 

- shm 从 shm_attach() 获得的共享内存段。

**返回值:** 

成功时返回 true， 或者在失败时返回 false。


### 获取共享内存变量 shm_get_var

**语法:**

`shm_get_var(SysvSharedMemory $shm, int $key): mixed`

**功能:** 

shm_get_var() 返回给定共享内存段中具有给定键的变量。该变量仍然存在于共享内存中。

**参数:** 

- shm 从 shm_attach() 获得的共享内存段。
- key 可变键。

**返回值:** 

返回具有给定键的变量。

### 共享内存变量是否存在 shm_has_var

**语法:**

`shm_has_var(SysvSharedMemory $shm, int $key): bool`

**功能:** 

检查一个特定的键是否存在于一个共享内存段内。

**参数:** 

- shm  从 shm_attach() 获得的共享内存段。
- key 可变键。

**返回值:** 

如果条目存在则返回真，否则返回假

### 修改/添加 共享内存变量 shm_put_var

**语法:**

`shm_put_var(SysvSharedMemory $shm, int $key, mixed $value): bool`

**功能:** 

shm_put_var() 使用给定的键插入或更新值。

如果shm不是一个有效的SysV共享内存索引，或者没有足够的共享内存剩余来完成你的请求，将会发出警告（E_WARNING级别）。

**参数:** 

- shm 从 shm_attach() 获得的共享内存段。
- key 可变键。
- value 变量。所有serialize()支持的变量类型都可以被使用：通常这意味着所有的类型，除了资源和一些不能被序列化的内部对象。

**返回值:** 

成功时返回 true， 或者在失败时返回 false。

### 删除共享内存变量 shm_remove_var

**语法:**

`shm_remove_var(SysvSharedMemory $shm, int $key): bool`

**功能:** 

删除具有给定键的变量并释放占用的内存。

**参数:** 

- shm 从 shm_attach() 获得的共享内存段。
- key  可变键。

**返回值:** 

成功时返回 true， 或者在失败时返回 false。


### 删除共享内存 shm_remove

**语法:**

`shm_remove(SysvSharedMemory $shm): bool`

**功能:** 

shm_remove() 删除共享内存 shm。所有数据都将被销毁。

**参数:** 

- shm 从 shm_attach() 获得的共享内存段。

**返回值:** 

成功时返回 true， 或者在失败时返回 false。


## Code

### 计数器

通过 信号量 和 共享内存 实现一个计数器, 代码每执行一次， 计数器就 `+1`


```php
$key = ftok(__FILE__, "a");
$var_name = 1; // 变量键，整形的

$sem_id = sem_get($key);
$shm = shm_attach($key);

sem_acquire($sem_id);

if (!shm_has_var($shm, $var_name)) {
    shm_put_var($shm, $var_name, 0);
}
$var_value = shm_get_var($shm, $var_name);
++$var_value;
shm_put_var($shm, $var_name, $var_value);

print("当前计数器值为: {$var_value}\n");
shm_detach($shm);
```

