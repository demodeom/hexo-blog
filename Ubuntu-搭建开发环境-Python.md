---
title: Ubuntu 搭建开发环境-Python
date: 2021-11-24 10:26:59
tags:
    - Python
    - 开发环境
categories:
    - Ubuntu
---

Python 多版本管理 [pyenv](https://github.com/pyenv/pyenv)

<!-- more -->

## 安装 pyenv

```bash
git clone https://hub.fastgit.org/pyenv/pyenv.git ~/.pyenv
```

## 配置 pyenv

```
export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"    # if `pyenv` is not already on PATH
eval "$(pyenv init --path)"
eval "$(pyenv init -)"
```

## 安装 Python

### 安装依赖

[编译依赖](https://github.com/pyenv/pyenv/wiki#suggested-build-environment)

Ubuntu

```bash
sudo apt-get update; sudo apt-get install make build-essential libssl-dev zlib1g-dev \
libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm \
libncursesw5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev
```

### 安装 Python

```bash
pyenv install 3.10.0
```

设置默认 Python 版本

```bash
pyenv default 3.10.0
```

## pip

安装

```
curl -o https://bootstrap.pypa.io/get-pip.py get-pip.py
python get-pip.py
```

国内源

```bash
pip config --global set global.index-url https://mirrors.aliyun.com/pypi/simple/

pip config --global set install.trusted-host mirrors.aliyun.com
```

Linux 使用次命令可能会有权限问题， 解决方法： 使用 `--user` 代替 `--global`

```bash
pip config --user set global.index-url https://mirrors.aliyun.com/pypi/simple/

pip config --user set install.trusted-host mirrors.aliyun.com
```

参考文章

- [pip全局设置镜像源](https://blog.csdn.net/u013771353/article/details/103150362)