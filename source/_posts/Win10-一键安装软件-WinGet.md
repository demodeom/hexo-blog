---
title: Win10 一键安装软件 WinGet
date: 2021-07-12 06:21:04
tags: Win10 Win11 WinGet
---

Windowns 终于迎来了一款软件管理神器-WinGet

<!-- more -->

## 常见问题

### 问题1

错误信息: `因为在此系统上禁止运行脚本`

解决方案: 以管理员身份运行 `Windows PowerShell` , 执行以下命令

```
set-ExecutionPolicy RemoteSigned
```

完整错误如下

```
PS D:\demo\hexo-blog> hexo s
hexo : 无法加载文件 C:\Users\Demo\AppData\Roaming\npm\hexo.ps1，因为在此系统上禁止运行脚本。有关详细信息
，请参阅 https:/go.microsoft.com/fwlink/?LinkID=135170 中的 about_Execution_Policies。
所在位置 行:1 字符: 1
+ hexo s
+ ~~~~
    + CategoryInfo          : SecurityError: (:) []，PSSecurityException
    + FullyQualifiedErrorId : UnauthorizedAccess
```

### 问题2

错误信息: `InternetOpenUrl() failed.`
解决方案: 自己解决 GitHub 访问问题

完整错误信息

```
PS C:\Users\Demo> winget install 7zip.7zip --rainbow
已找到 7-Zip [7zip.7zip]
此应用程序由其所有者授权给你。
Microsoft 对第三方程序包概不负责, 也不向第三方程序包授予任何许可证。
Downloading https://raw.githubusercontent.com/ip7z/a/main/7z1900-x64.msi
执行此命令时发生意外错误：
InternetOpenUrl() failed.
0x80072ee7 : unknown error
```

### 问题3

错误信息： `安装程序哈希不匹配；若要覆盖此检查, 请使用 --force`
原因： 最新版的软件下载地址不区分版本, winget 仓库的程序哈希更新存在滞后性
解决方法： `winget install Google.Chrome --force` --rainbow

完整错误信息如下

```
PS C:\Users\Demo> winget install Google.Chrome --rainbow
已找到 Google Chrome [Google.Chrome]
此应用程序由其所有者授权给你。
Microsoft 对第三方程序包概不负责, 也不向第三方程序包授予任何许可证。
Downloading https://dl.google.com/edgedl/chrome/install/ --rainbowGoogleChromeStandaloneEnterprise64.msi
 ██████████████████████████████ 76.2 MB / 76.2 MB
安装程序哈希不匹配；若要覆盖此检查, 请使用 --force
```

### 错误4

错误信息： `Installer failed with exit code: 1`

原因: 新版win10支持修改默认软件安装位置，


完整错误信息

```
PS C:\Users\Demo> winget install Oracle.VirtualBox  --rainbow
已找到 Oracle VM VirtualBox [Oracle.VirtualBox]
此应用程序由其所有者授权给你。
Microsoft 对第三方程序包概不负责，也不向第三方程序包授予任何许可证。
Downloading https://download.virtualbox.org/virtualbox/6.1.26/VirtualBox-6.1.26-145957-Win.exe
  ██████████████████████████████   103 MB /  103 MB
已成功验证安装程序哈希
正在启动程序包安装...
Installer failed with exit code: 1
```


## 压缩 7zip

```bash
winget install 7zip.7zip --rainbow
```

## Firefox

```bash
winget install Mozilla.Firefox --rainbow
```

常用扩展

- LastPass Password Manager
- Infinity新标签页(Pro)
- uBlock Origin
- 划词翻译 作者： Mica

## Google Chrome

```bash
winget install Google.Chrome --force --rainbow
```

## QQ 

```bash
winget install Tencent.QQ --rainbow
```

## 微信 

```bash
winget install Tencent.WeChat --force --rainbow
```

## TIM

```bash
winget install Tencent.TIM --rainbow
``` 


## XDM

```bash
winget install subhra74.XtremeDownloadManager --rainbow
```

## 迅雷

```bash
winget install XunLei.xunlei --rainbow
```



## SublimeText4

```
winget install SublimeHQ.SublimeText.4 --rainbow
```

## WebStorm

```bash
winget install JetBrains.WebStorm --rainbow
```

## PHPStorm

```bash
winget install JetBrains.PHPStorm --rainbow
```

## DataGrip

```bash
winget install JetBrains.DataGrip --rainbow
```

## CLion

```bash
winget install JetBrains.CLion --rainbow
```

## Node.js

```bash
winget install OpenJS.NodeJSLTS --rainbow
```

## Python

```bash
winget install Python.Python.3  --rainbow
```

## 视频 PotPlayer

```bash
winget install Daum.PotPlayer --rainbow
```

## 音乐 AIMP

```bash
winget install AIMP.AIMP --rainbow
```

## 云梯 Qv2ray

```bash
winget install Qv2ray.Qv2ray  --rainbow
```

[V2ray Core](https://hub.fastgit.org/v2fly/v2ray-core/releases)

## VirtualBox

```bash
winget install Oracle.VirtualBox  --rainbow
```

## WorkstationPro

```bash
winget install VMware.WorkstationPro  --rainbow
```

<!-- Vmware Workstation16 许可证密钥 -->
<!-- ZF3R0-FHED2-M80TY-8QYGC-NPKYF -->
<!-- YF390-0HF8P-M81RQ-2DXQE-M2UT6 -->
<!-- ZF71R-DMX85-08DQY-8YMNC-PPHV8 -->
<!-- ------------------------------ -->

## Vagrant

```
winget install Hashicorp.Vagrant --force   --rainbow
```

[Find Box](https://app.vagrantup.com/boxes/search)



## 截图录屏 Capture

```bash
winget install FastStone.Capture  --rainbow
```
<!-- FastStone Capture 注册码 序列号： -->
<!-- 用户名: bluman -->
<!-- 注册码: VPISCJULXUFGDDXYAUYF -->

## 火焰截图

```bash
winget install Flameshot.Flameshot  --rainbow
```
<!-- TBD  -->
[Snipaste](https://zh.snipaste.com/) 

## Git

```bash
winget install Git.Git --rainbow
```

vagrant box add laravel/homestead