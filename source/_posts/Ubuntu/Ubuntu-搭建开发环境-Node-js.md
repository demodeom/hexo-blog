---
title: Ubuntu 搭建开发环境-Node.js
date: 2021-11-24 10:27:12
tags:
    - Node.js
    - 开发环境
categories:
    - Ubuntu
---

Node.js 多版本管理 [nvm](https://github.com/nvm-sh/nvm)

<!-- more -->

## 安装 nvm

一键安装

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
```

手动安装

```bash
curl https://raw.fastgit.org/nvm-sh/nvm/v0.39.0/install.sh -o nvm-install.sh
chmod +x ./nvm-install.sh
sed -i 's/https:\/\/github.com/https:\/\/hub.fastgit.org/g' nvm-install.sh
./nvm-install.sh
```

## 配置 nvm

```bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
```

## 常用命令

`nvm install --lts` Install the latest LTS version

`nvm use --lts` Use the latest LTS version

## 淘宝镜像

```
npn install nrm -g
nrm use taobao
```