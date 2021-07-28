---
title: Ubuntu Server安装 VirtualBox 增强功能
date: 2021-07-08 18:46:43
tags: 
	- Ubuntu 
	- VirtualBox
---


Ubuntu Server安装 VirtualBox 增强功能


<!-- more -->

## 安装增强功能

**Step 1**: 设备 -> 安装增强功能

![](http://blog.image.codedemo.vip/vmbox-additions.PNG)



**Step 2**: 挂载镜像

```bash
sudo mount /dev/cdrom /mnt
```

**Set 3**: 安装增强功能

```bash
cd /mnt

sudo ./VBoxLinuxAdditions.run
```

**Step 4**: 设置共享文件

![](http://blog.image.codedemo.vip/vmbox-share.png)

**Step 5**: 点击 `ok` 按钮

**Step 6**: 重启系统

```bash
sudo shutdown -r now
```

## 权限问题

共享目录权限如下:

```bash
drwxrwx--- 1 root vboxsf       0 Jul  8 10:03 share
```

解决方案： 将当前用户添加到 vboxsf 分组

```bash
sudo usermod -aG vboxsf us
```

重启生效