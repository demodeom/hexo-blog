---
title: WinGet
date: 2021-07-12 06:21:04
tags:
    - Win
    - WinGet
---

Windowns 终于迎来了一款软件管理神器-WinGet

<!-- more -->

## 常用软件

因为部分软件安装包下载地址不规范，导致安装包签名不一致， 所以使用 ` --force` 参数

目前不支持安装路径的统一配置， 但是可以通过 `-l` 指定安装路径，确保安装目录已经被创建

并不是所有的软件都支持自定义安装路径

- **压缩** 7zip `winget install 7zip.7zip -l D:/WinGetApp --force --rainbow`

- **浏览器**  Firefox `winget install Mozilla.Firefox -l D:/WinGetApp --force --rainbow`

- **浏览器**  Google Chrome`winget install Google.Chrome -l D:/WinGetApp --force --rainbow`

- **浏览器**  Opera`winget install Opera.Opera -l D:/WinGetApp --force --rainbow`

- **聊天** QQ `winget install Tencent.QQ -l D:/WinGetApp --force --rainbow`

- **聊天** 微信 `winget install Tencent.WeChat -l D:/WinGetApp --force --rainbow`

- **聊天** TIM `winget install Tencent.TIM -l D:/WinGetApp --force --rainbow`

- **下载工具** XDM `winget install subhra74.XtremeDownloadManager -l D:/WinGetApp --force --rainbow`

- **下载工具** 迅雷 `winget install XunLei.xunlei -l D:/WinGetApp --force --rainbow`

- **IDE** SublimeText4 `winget install SublimeHQ.SublimeText.4 -l D:/WinGetApp --force --rainbow`

- **IDE** WebStorm `winget install JetBrains.WebStorm -l D:/WinGetApp --force --rainbow`

- **IDE** PHPStorm `winget install JetBrains.PHPStorm -l D:/WinGetApp --force --rainbow`

- **IDE** CLion `winget install JetBrains.CLion -l D:/WinGetApp --force --rainbow`

- **IDE** Typora `winget install Typora.Typora -l D:/WinGetApp --force --rainbow`

- **开发环境** Node.js `winget install OpenJS.NodeJSLTS -l D:/WinGetApp --force --rainbow`

- **开发环境** Python `winget install Python.Python.3 -l D:/WinGetApp --force --rainbow`

- **视频播放器** PotPlayer `winget install Daum.PotPlayer -l D:/WinGetApp --force --rainbow`

- **音乐播放器**  AIMP `winget install AIMP.AIMP -l D:/WinGetApp --force --rainbow`

- **VPN** Qv2ray `winget install Qv2ray.Qv2ray -l D:/WinGetApp --force --rainbow`

- **虚拟机** VirtualBox `winget install Oracle.VirtualBox -l D:/WinGetApp --force --rainbow`

- **虚拟机** WorkstationPro `winget install VMware.WorkstationPro -l D:/WinGetApp --force --rainbow`

- **截图录屏** Capture `winget install FastStone.Capture -l D:/WinGetApp --force --rainbow`

- **截图**  Flameshot `winget install Flameshot.Flameshot -l D:/WinGetApp --force --rainbow`

- **网盘** 百度网盘 `winget install baidu.baidunetdisk -l D:/WinGetApp --force --rainbow`

- **版本管理** Git `winget install Git.Git -l D:/WinGetApp --force --rainbow`

- **翻译** DeepL `winget install DeepL.DeepL -l D:/WinGetApp --force --rainbow`

<!-- Vmware Workstation16 许可证密钥 -->
<!-- ZF3R0-FHED2-M80TY-8QYGC-NPKYF -->
<!-- YF390-0HF8P-M81RQ-2DXQE-M2UT6 -->
<!-- ZF71R-DMX85-08DQY-8YMNC-PPHV8 -->
<!-- ------------------------------ -->

<!-- FastStone Capture 注册码 序列号： -->
<!-- 用户名: bluman  -->
<!-- 注册码: VPISCJULXUFGDDXYAUYF  -->


## 常见问题

### 问题1

错误信息: `因为在此系统上禁止运行脚本`

解决方案: 以管理员身份运行 CMD ，执行以下命令

```
set-ExecutionPolicy RemoteSigned
```

### 问题2

错误信息: 由于无法访问 GitHub 导致软件安装包下载失败
解决方案: 自己解决 GitHub 访问问题
