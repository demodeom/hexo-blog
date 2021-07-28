---
title: Win10 一键安装软件 WinGet
date: 2021-07-12 06:21:04
tags: Win10 Win11 WinGet
---

Windowns 终于迎来了一款软件管理神器-WinGet

<!-- more -->

## 已知问题

### 问题1

错误信息: `因为在此系统上禁止运行脚本`

解决方案: 以管理员身份运行 CMD ，执行以下命令

```
set-ExecutionPolicy RemoteSigned
```

### 问题2

错误信息: 由于无法访问 GitHub 导致软件安装包下载失败
解决方案: 自己解决 GitHub 访问问题


## 基础软件

### 压缩 7zip

```bash
winget install 7zip.7zip   --rainbow 
```

## 浏览器

### Firefox

```bash
winget install Mozilla.Firefox  --rainbow 
```

### Google Chrome

```bash
winget install Google.Chrome   --rainbow
```

### Opera

```bash
winget install Opera.Opera  --rainbow
```

## 聊天

### QQ 

```bash
winget install Tencent.QQ
```

### 微信 

```bash
winget install Tencent.WeChat --force
```

### TIM

```bash
winget install Tencent.TIM
``` 

## 下载工具

### XDM

```bash
winget install subhra74.XtremeDownloadManager   --rainbow 
```

### 迅雷

```bash
winget install XunLei.xunlei  --rainbow 
```

## IDE

### SublimeText4

```
winget install SublimeHQ.SublimeText.4   --rainbow 
```

### WebStorm

```bash
winget install JetBrains.WebStorm   --rainbow 
```

### PHPStorm

```bash
winget install JetBrains.PHPStorm   --rainbow 
```

### CLion

```bash
winget install JetBrains.CLion
```

## 开发环境

### Node.js

```bash
winget install OpenJS.NodeJSLTS  --rainbow 
```

### Python

```bash
winget install Python.Python.3 --rainbow 
```

## 音视频

### 视频 PotPlayer

```bash
winget install Daum.PotPlayer   --rainbow 
```

### 音乐 AIMP

```bash
winget install AIMP.AIMP   --rainbow 
```

## 上网

```bash
winget install Qv2ray.Qv2ray --rainbow 
```

## 虚拟机

### VirtualBox

```bash
winget install Oracle.VirtualBox --rainbow 
```

### WorkstationPro

```bash
winget install VMware.WorkstationPro --rainbow 
```

<!-- Vmware Workstation16 许可证密钥 -->
<!-- ZF3R0-FHED2-M80TY-8QYGC-NPKYF -->
<!-- YF390-0HF8P-M81RQ-2DXQE-M2UT6 -->
<!-- ZF71R-DMX85-08DQY-8YMNC-PPHV8 -->
<!-- ------------------------------ -->

## 图片

### 截图录屏 Capture

```bash
winget install FastStone.Capture --rainbow 
```
<!-- FastStone Capture 注册码 序列号： -->
<!-- 用户名: bluman  -->
<!-- 注册码: VPISCJULXUFGDDXYAUYF  -->

### 火焰截图

```bash
winget install Flameshot.Flameshot --rainbow 
```