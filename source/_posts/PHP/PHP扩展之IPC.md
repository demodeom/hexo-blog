---
title: PHP扩展之IPC
date: 2021-08-07 05:36:02
tags:
    - 共享内存
categories:
    - PHP
---


PHP操作消息队列

<!-- more -->

## 定义

> IPC（Inter-Process Communication，进程间通信）。进程间通信是指两个进程的数据之间产生交互

## 安装

使用 ` --enable-sysvmsg` 编译 PHP

检查 `sysvsem` 是否安装

```bash
➜  ~ php -m | grep sysvmsg
sysvmsg
```



## 主要函数

- msg_get_queue - 创建或附加到一个消息队列上
- msg_queue_exists - 检查一个消息队列是否存在
- msg_receive - 从一个消息队列中接收一个消息
- msg_remove_queue - 销毁一个消息队列
- msg_send - 向一个消息队列发送一条消息
- msg_set_queue - 设置消息队列数据结构中的信息
- msg_stat_queue - 返回来自消息队列数据结构的信息


### msg_get_queue

**语法:**

`msg_get_queue(int $key, int $permissions = 0666): SysvMessageQueue|false`

**功能:** 

msg_get_queue()返回一个ID，可用于访问具有给定键的System V消息队列。第一次调用会创建有可选权限的消息队列。第二次调用msg_get_queue()相同的键会返回一个不同的消息队列标识符，但这两个标识符访问的是同一个底层消息队列。

**参数:** 

- key  消息队列数字 ID
- permissions 队列权限。默认为0666。如果消息队列已经存在，权限将被忽略。

**返回值:** 

返回SysvMessageQueue实例，可用于访问System V消息队列，或者在失败时返回false。

### msg_queue_exists

**语法:**

`msg_queue_exists(int $key): bool`

检查消息队列键是否存在。

**参数:** 

- key 队列键。

**返回值:** 

成功时返回 true， 或者在失败时返回 false。



### msg_receive

**语法:**

`msg_receive(
    SysvMessageQueue $queue,
    int $desired_message_type,
    int &$received_message_type,
    int $max_message_size,
    mixed &$message,
    bool $unserialize = true,
    int $flags = 0,
    int &$error_code = null
): bool`

**功能:** 

msg_receive() 将接收来自desired_message_type 指定类型的指定队列的第一条消息。

**参数:** 

- queue 消息队列。
- desired_message_type 如果desired_message_type为0，则返回队列中最前面的那个消息。如果desired_message_type大于0，那么将返回该类型的第一条消息。如果desired_message_type小于0，将读取队列中第一个类型小于或等于desired_message_type绝对值的消息。如果没有符合条件的消息，你的脚本将等待，直到队列中出现合适的消息。你可以通过在flags参数中指定MSG_IPC_NOWAIT来防止脚本阻塞。
- received_message_type 收到的信息的类型将被存储在这个参数中。
- max_message_size 要接受的消息的最大尺寸由max_message_size指定；如果队列中的消息大于这个尺寸，该函数将失败（除非你设置了下面描述的标志）。
- message 收到的信息将被储存在信息中，除非接收信息时有错误。
- unserialize 如果设置为 "true"，消息会被当作使用与会话模块相同的机制进行序列化处理。消息将被取消序列化，然后返回给你的脚本。这允许你轻松地从其他PHP脚本中接收数组或复杂的对象结构，或者如果你使用WDDX序列化器，从任何WDDX兼容的源中接收。如果 unserialize 为 false，则消息将作为二进制安全字符串返回。
- flags 可选标志允许您将标志传递给低级 msgrcv 系统调用。它默认为 0，但您可以指定一个或多个以下值（通过将它们相加或或运算在一起）。
- error_code 如果函数失败，可选的 error_code 将被设置为系统 errno 变量的值。



<center><b>msg_receive 的 flag</b></center>

|标志|描述|
|:---|:---|
|MSG_IPC_NOWAIT|如果没有所需信息类型的信息，立即返回，不要等待。该函数将失败，并返回一个对应于MSG_ENOMSG的整数值。|
|MSG_EXCEPT|将此标志与大于 0 的 desired_message_type 结合使用将导致函数接收不等于 desired_message_type 的第一条消息。|
|MSG_NOERROR|如果消息长于max_message_size，设置这个标志将把消息截断到max_message_size，并且不会发出错误信号。|


**返回值:** 


成功时返回 true， 或者在失败时返回 false。

成功完成后，消息队列数据结构更新如下：msg_lrpid 设置为调用进程的进程 ID，msg_qnum 减 1，msg_rtime 设置为当前时间。


### msg_remove_queue

**语法:**

msg_remove_queue(SysvMessageQueue $queue): bool

**功能:** 

msg_remove_queue() 销毁队列指定的消息队列。只有当所有进程都完成了消息队列的工作并且需要释放它所持有的系统资源时才使用此功能。

**参数:** 

- queue 消息队列。

**返回值:** 

成功时返回 true， 或者在失败时返回 false。

### msg_send

**语法:**

`msg_send(
    SysvMessageQueue $queue,
    int $message_type,
    string|int|float|bool $message,
    bool $serialize = true,
    bool $blocking = true,
    int &$error_code = null
): bool`

**功能:** 

msg_send() 将 message_type 类型的消息（必须大于 0）发送到 queue 指定的消息队列。

**参数:** 

- queue 消息队列。
- message_type 消息的类型（必须大于 0）
- message 消息的正文。
- serialize 可选的序列化控制消息的发送方式。 serialize 默认为 true，这意味着消息在发送到队列之前使用与会话模块相同的机制进行序列化。这允许将复杂的数组和对象发送到其他 PHP 脚本，或者如果您使用的是 WDDX 序列化程序，则可以发送到任何 WDDX 兼容客户端。
- blocking 如果消息太大而无法放入队列，您的脚本将等待另一个进程从队列中读取消息并释放足够的空间来发送您的消息。这称为阻塞；您可以通过将可选的阻塞参数设置为 false 来防止阻塞，在这种情况下，如果消息对于队列来说太大，msg_send() 将立即返回 false，并将可选的 error_code 设置为 MSG_EAGAIN，表示您应该尝试发送您的消息再过一会儿。
- error_code 如果函数失败，可选的错误代码将被设置为系统 errno 变量的值。


如果提供的serialize设置为false，必须是字符串、int、float或bool类型。在其他情况下，将发出一个警告。

**返回值:** 

成功时返回 true， 或者在失败时返回 false。

成功完成后，消息队列数据结构被更新如下：msg_lspid被设置为调用进程的进程ID，msg_qnum被递增1，msg_stime被设置为当前时间。


### msg_set_queue

**语法:**

`msg_set_queue(SysvMessageQueue $queue, array $data): bool`

**功能:** 

msg_set_queue() 允许您更改底层消息队列数据结构的 msg_perm.uid、msg_perm.gid、msg_perm.mode 和 msg_qbytes 字段的值。

改变数据结构将要求 PHP 以创建队列的同一用户身份运行，拥有该队列（由现有的 msg_perm.xxx 字段决定），或以 root 权限运行。要将 msg_qbytes 的值提高到系统定义的极限以上，需要 root 权限。

**参数:** 

- queue 消息队列。
- data 你通过设置数据数组中你需要的键值来指定你所需要的值。

成功时返回 true， 或者在失败时返回 false。

**语法:**


`msg_stat_queue(SysvMessageQueue $queue): array|false`

**功能:** 

msg_stat_queue() 返回队列指定的消息队列的消息队列元数据。例如，这有助于确定哪个进程发送了刚刚收到的消息。

**参数:** 

- queue 消息队列。

**返回值:** 

成功时，返回值是一个数组，其键和值具有以下含义：


<center><b>msg_stat_queue 的数组结构</b></center>

|字段|描述|
|:---|:---|
|msg_perm.uid  |  队列所有者的 uid。|
|msg_perm.gid  |  队列所有者的 gid。|
|msg_perm.mode |  队列的文件访问模式。|
|msg_stime|   最后一条消息发送到队列的时间。|
|msg_rtime|   从队列中接收到最后一条消息的时间。|
|msg_ctime|   上次更改队列的时间。|
|msg_qnum|    等待从队列中读取的消息数。|
|msg_qbytes|  一个消息队列中允许的最大字节数。在 Linux 上，可以通过 /proc/sys/kernel/msgmnb 读取和修改此值。|
|msg_lspid|   将最后一条消息发送到队列的进程的 pid。|
|msg_lrpid|   从队列接收最后一条消息的进程的 pid。|