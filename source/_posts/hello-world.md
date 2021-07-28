---
title: Hello Hexo
tags: Hexo
---

欢迎来到Hexo! 这是你的第一个帖子。请查看文档以了解更多信息。如果你在使用Hexo时遇到任何问题，你可以在故障排除中找到答案，或者你可以在GitHub上问我。

## 快速开始

### 创建项目

创建项目

```bash
mkdir blog
```

初始化项目

``` bash
cd blog
hexo init
```

### 自动部署

自动部署扩展

```bash
npm install hexo-deployer-git --save
```

自动部署配置: `blog/_config.yml`

```
deploy:
  type: 'git'
  repo: 'git@github.com:demodeom/demodeom.github.io.git'
```

## 主题 Aurora

### 安装主题

安装主题

```bash
npm install hexo-theme-aurora --save
```

导出主题配置

```bash
cp -rf ./node_modules/hexo-theme-aurora/_config.yml ./_config.aurora.yml
```

使用主题: `blog/_config.yml`

```yml
theme: aurora
```

### 配置主题

路由 `blog/_config.yml`

```yml
permalink: /post/:title.html
```

代码高亮

```yml
highlight:
  enable: false
prismjs:
  enable: true
  preprocess: false
```

### 完善页面

创建关于页面

```bash
hexo new page about
```

<!-- 原本想体验下 Gitalk ， 由于国内环境，体验遇到重重困难 -->

### 细节配置

`blog/_config.aurora.yml`

配置导航

```yml
menu:
  About: true
  Tags: true
  Archives: true
```

夜间模式(默认关闭)

```yml
theme:
  dark_mode: false
```

网站配置

```yml
site:
  subtitle: Demo's Blog
  author: Demo
  nick: Demodeom
  description:
  link: '/'
  language: cn
  multi_language: true
  logo: https://img-blog.csdnimg.cn/20210313122054101.png
  avatar: https://img-blog.csdnimg.cn/20210313122054101.png
  beian:
    number: ''
    link: ''
  police_beian:
    number: ''
    link: ''
```

## 机器人

```yml
# 开启 Aurora 机器人 Dia
aurora_bot:
  # ======================================================
  # 设置为 true，你可爱的机器人就会启动。
  # ======================================================
  enable: true
  # ======================================================
  # 该机器人支持两种语言
  # -- en: 英语
  # -- cn: 中文
  # ======================================================
  locale: cn
  # ======================================================
  # 目前只支持使用 Dia，将来会支持 live2d。
  # ======================================================
  bot_type: dia
```

完整配置参考 [机器人配置](https://aurora.tridiamond.tech/zh/guide/plugins.html#%E6%9C%BA%E5%99%A8%E4%BA%BA%E9%85%8D%E7%BD%AE)


## Git 忽略文件

`blog/.gitignore`

```
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
public/
.deploy*/
.idea
```