---
title: PHP扩展之信号量
date: 2021-08-07 04:41:16
tags:
    - 信号量
categories:
    - PHP
---

PHP操作信号量

<!-- more -->

## 定义

> 信号量(Semaphore), 有时被称为信号灯, 是在多线程环境下使用的一种设施, 是可以用来保证两个或多个关键代码段不被并发调用.  
> 在进入一个关键代码段之前, 线程必须获取一个信号量；一旦该关键代码段完成了, 那么该线程必须释放信号量.  
> 其它想进入该关键代码段的线程必须等待直到第一个线程释放信号量.  
> 为了完成这个过程, 需要创建一个信号量VI, 然后将Acquire Semaphore VI以及Release Semaphore VI分别放置在每个关键代码段的首末端.  确认这些信号量VI引用的是初始创建的信号量.  

## 安装

使用 ` --enable-sysvsem` 编译 PHP

检查 `sysvsem` 是否安装

```bash
➜  ~ php -m | grep sysvsem
sysvsem
```

## 主要函数

- ftok - 将路径名和项目标识符转换成System V IPC密钥
- sem_get 获取信号量
- sem_release — 释放信号量
- sem_remove — 删除信号量

### 生成标识符 ftok

**语法:**

`ftok(string $filename, string $project_id): int`

**功能:** 

该函数将现有可访问文件的文件名和项目标识符转换为一个整数, 用于例如shmop_open()和其他System V IPC键.  

**参数:** 

- filename 可访问文件的路径.  
- project_id 项目标识符.  这必须是一个单字符的字符串.  

**返回值:** 

成功时返回值将是创建的键值, 否则返回 -1

**笔记:**

如果你打算用ftok()来生成一个IPC标识符与其他应用程序共享, 请注意PHP使用proj参数的ASCII值来生成密钥, 而不是proj（又称id）参数本身.  这样做的结果是, 如果你在PHP端使用 "1 "作为id, 你就需要在其他地方使用49.  这可能不是所有操作系统的情况, 但对于FreeBSD来说肯定是这样, 它要求传递给ftok的id参数是一个int.  另外值得注意的是, ipcs 和 ipcrm 对于调试 SysV 队列等非常有用.  

~~由于 ftok 仅使用文件 inode 的最后 16 位, 因此您可能会在大型文件系统上发生冲突.  不幸的是, 在大型文件系统上, 您可能会很快发生冲突：如果您有 350-400 个文件的集合, 那么其中两个文件的 inode 很可能具有相同的最后 16 位.  所以我已经开始使用 fileinode 而不是 ftok 和 shmop_open 之类的函数.  ~~ 这是 14 年前(2021-08-07)笔记,  经过验证,  并不准确, 也许PHP已经不是 14 年前的PHP

你不必使用ftok()来指定System V IPC标识符, 尽管这样做是件好事.  向msg_get_queue等传递一个普通的int, 只要你在读/写时使用该值, 就会有同样的效果.  我把它用于一些生成小队列的小任务.  

这个函数既不是 ext/sysvsem 的一部分, 也不是 ext/sysvshm 的一部分, 而是伴随着 PHP 的核心函数（来自 ext/standard）.  



### 创建信号量 sem_get

**语法:**

`sem_get(int $key,int $max_acquire = 1,int $permissions = 0666,bool $auto_release = true): SysvSemaphore|false`

**功能:** 


sem_get()返回一个id, 可以用来访问带有给定键的System V信号.  

对同一个键再次调用sem_get()将返回一个不同的信号灯标识符, 但是这两个标识符都是访问同一个基础信号灯.  

如果key为0, 每次调用sem_get()都会创建一个新的私有信号.  

**参数:** 

- key
- max_acquire 可以同时获取信号的进程的数量被设置为max_acquire.  
- permissions semaphore的权限.  实际上这个值只有在进程发现它是当前唯一连接到semaphore的进程时才会设置.  
- auto_release 指定是否应该在请求关闭时自动释放信号.  

**返回值:** 

成功时返回一个肯定的semaphore标识符, 错误时返回false.  

当使用sem_get()函数访问在PHP之外创建的信号灯时, 注意该信号灯必须是作为3个信号灯的集合来创建的（例如, 在调用C语言的semget()函数时指定3作为nsems参数）, 否则PHP将无法访问该信号灯.  


### 获取信号量 sem_acquire

**语法:**

`sem_acquire(SysvSemaphore $semaphore, bool $non_blocking = false): bool`

**功能:** 

sem_acquire() 默认情况下会阻塞（如有必要）, 直到可以获取信号量.  如果获取信号量会导致超过其最大信号量数, 则尝试获取已获取的信号量的进程将永远阻塞.  

处理请求后, 进程获取但未明确释放的任何信号量将自动释放并生成警告.  

**参数:** 

- semaphore 是从 sem_get() 获得的信号量.  
- non_blocking 指定进程是否不应该等待semaphore被获取.  如果设置为 "true", 在不能立即获得信号的情况下, 该调用将立即返回 "false".  

**笔记:**

参数 `non_blocking` 让信号量的获取变得非常灵活, 你可以：

- 设置 non_blocking 值为 false ,  以阻塞的方式一直等待
- 设置 non_blocking 值为 true, 不等待, 直接处理
- 设置 non_blocking 值为 true, 自定义等待时间

自定义等待时间

```php
function semAcquire($sign, $wait_seconds=10){
    $result = false;
    for ($i=0; $i < $wait_seconds; $i++) { 
        $result = sem_acquire($sign, true);
        if ($result) {
            break;
        }
        sleep(1);
    }
    return $result;
}
```

### 释放信号量 sem_release

**语法:**

`sem_release(SysvSemaphore $semaphore): bool`

**功能:** 

sem_release()释放当前被调用进程获取的信号, 否则会产生一个警告.  

释放信号后, 可以调用sem_acquire()来重新获取信号.  

**参数:** 

- semaphore `sem_get()` 返回的信号量.  

**返回值:** 

成功时返回 true,  或者在失败时返回 false.  

**笔记:**

当获取信号量的进城意外终止时, 会自动释放信号量.  (减少了开发工作量)


### 删除信号量 sem_remove

**语法:**

`sem_remove(SysvSemaphore $semaphore): bool`

**功能:** 

sem_remove() 删除给定的信号量.   删除信号量后, 它不再可访问.  

**参数:** 

- semaphore `sem_get()` 返回的信号量.  


**返回值:** 

成功时返回 true,  或者在失败时返回 false.  


## Code

### Example

信号量简单使用

```php 

// 通过 sleep 模拟实际业务
function doSomeThing($seconds = 30)
{
    print("信号量获取成功, 处理业务： \n");
    for ($i=0; $i < $seconds; $i++) { 
        sleep(1);
    }
}

function isTrue($real_value, $expect_value, $error_msg){
    if ($real_value === $expect_value) {
        throw new Exception($error_msg, 1);
    }
}

function main()
{
    // 生成唯一标识
    $k = ftok(__FILE__, 'A');
    isTrue($k, -1, "标识符生成失败");

    // 信号量创建
    $sign = sem_get($k);
    isTrue($sign, false, "信号量创建失败");

    // 信号量获取
    $acquire_res = sem_acquire($sign, true);
    isTrue($acquire_res, false, "信号量获取失败");
    
    // 模拟实际业务
    if ($acquire_res) {
        doSomeThing(15);
    }

    // 释放信号量
    $release_res = sem_release($sign);
    isTrue($acquire_res, false, "信号量释放失败");
}

main();
```

### Ftok 冲突验证

为了简化代码,  使用 redis 的 set 来去重

**测试结果 10,000 个文件, 并未出现冲突** 

```php
// 生成文件名
function getFileName()
{
    $file_name = str_replace(".", "", microtime(true));
    $file_name .= random_int(0, 99999);
    $file_name .= random_int(0, 99999);
    $file_name .= random_int(0, 99999);
    return $file_name;
}

// 删除测试目录
function removeDir($dir_name)
{
    $dir = opendir($dir_name);
    while(false !== ( $file = readdir($dir)) ) {
        if (( $file != '.' ) && ( $file != '..' )) {
            $full = $dir_name . '/' . $file;
            if ( is_dir($full) ) {
                rrmdir($full);
            }
            else {
                unlink($full);
            }
        }
    }
    closedir($dir);
    rmdir($dir_name);
}

// 创建临时目录,  存放测试文件
function createTmpDir($dir_name)
{
    if (file_exists($dir_name)) {
        removeDir($dir_name);
    }
    mkdir($dir_name);
}

// 创建文件,  供 ftok 函数使用 
function createFile($tmp_dir)
{
    $file_name = getFileName();
    $file_path = $tmp_dir.'/'.$file_name;
    if (!file_exists($file_path)) {
        touch($file_path);
        return $file_path;
    }
    return false;
}

// 获取 key
function getFtokId($file_path)
{
    $k = ftok($file_path, 'A');
    if ($k === -1) {
        throw new Exception("标识符生成失败", 1);
    }
    return $k;
}

function redisConnect()
{
    $redis = new Redis();
    $redis->connect('127.0.0.1', 6379);
    return $redis;
}

function main($total_file=10)
{
    $tmp_dir = "./test_ftok_dir";
    $redis_ftok_set = "redis_ftok_set";

    createTmpDir($tmp_dir);
    $redis = redisConnect();

    $redis->del($redis_ftok_set);

    $total_file_num = 0;

    for ($i=0; $i < $total_file; $i++) { 

        $file_path = createFile($tmp_dir);
        
        if ($file_path) {
            $k = getFtokId($file_path);
            $redis->sadd($redis_ftok_set, $k);
            ++$total_file_num;
        }
    }
    $total_ftok_id = $redis->sCard($redis_ftok_set);

    $redis->close();

    print("total_file: {$total_file}\n");
    print("total_file_num: {$total_file_num}\n");
    print("total_ftok_id: {$total_ftok_id}\n");
}

main(10000);
```

结果如下

```
total_file: 10000
total_file_num: 10000
total_ftok_id: 10000
```

## 应用

- 进程 内存共享
- 雪花算法

## 其他

在 ftok 笔记中发现 ipcs 和 ipcrm 命令


简而言之

- ipcs 用于查看 共享内存，消息队列，信号
- ipcrm 用于移除 共享内存，消息队列，信号

参考文章 [Linux ipcs命令与ipcrm命令的用法详解](https://blog.csdn.net/stpeace/article/details/68953669)