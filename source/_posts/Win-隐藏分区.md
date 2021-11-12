---
title: Win 隐藏分区
date: 2021-11-10 17:00:14
tags:
	- Win 
  - 分区
categories:
	- Win
---

Win 隐藏分区

<!-- more -->

## 第一步: 打开 diskpart

打开 CMD, 执行 `diskpart` 命令

```
PS C:\Users\wyj> diskpart

Microsoft DiskPart 版本 10.0.22000.1

Copyright (C) Microsoft Corporation.
在计算机上: DESKTOP-57R6QQU

DISKPART>
```


## 第二步: 列出磁盘列表

执行命令 `list disk`

```
  磁盘 ###  状态           大小     可用     Dyn  Gpt
  --------  -------------  -------  -------  ---  ---
  磁盘 0    联机              476 GB      0 B        *
  磁盘 1    联机              465 GB  1024 KB        *
  磁盘 2    联机             1863 GB  3072 KB
```

## 第三步: 选择磁盘

选择分 EFI 分区所在的磁盘, 比如: `select disk 0`

```
磁盘 0 现在是所选磁盘。
```


## 第四步:  列出分区列表

执行命令 `list volume`

```

  卷 ###      LTR  标签         FS     类型        大小     状态       信息
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  卷     0                      FAT32  磁盘分区         512 MB  正常         已隐藏
  卷     1     C   系统           NTFS   磁盘分区         120 GB  正常         启动
  卷     2     D   软件           NTFS   磁盘分区         345 GB  正常
  卷     3     Z                 FAT32  磁盘分区         497 MB  正常         系统
  卷     4     E                NTFS   磁盘分区        1863 GB  正常
```

## 第五步: 删除挂载点

以 `Z` 盘符为例; 执行命令 `select volume Z` 选中分区; 执行命令 `remove letter=Z` 删除挂载点


```
DISKPART> select volume z

卷 3 是所选卷。

DISKPART> remove letter z

DiskPart 成功地删除了驱动器号或装载点。
```

## 第六步: 关闭cmd

执行命令 `exit`

```
DISKPART> exit

退出 DiskPart...
```