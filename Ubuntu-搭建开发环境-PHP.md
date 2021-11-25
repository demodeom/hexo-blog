---
title: Ubuntu 搭建开发环境-PHP
date: 2021-11-24 10:26:39
tags:
    - PHP
    - 开发环境
categories:
    - Ubuntu
---

PHP 多版本安装管理

<!-- more -->


## 添加 PPA

[PPA 地址](https://launchpad.net/~ondrej/+archive/ubuntu/php)

```bash
sudo add-apt-repository ppa:ondrej/php
```

## 安装 PHP

### PHP 7.3

```bash
sudo apt install php7.3-fpm php7.3-cli php7.3-mbstring php7.3-zip php7.3-mysql php7.3-xml php7.3-gd php7.3-bcmath
```


### PHP 7.4

```bash
sudo apt install php7.4-fpm php7.4-cli php7.4-mbstring php7.4-zip php7.4-mysql php7.4-xml php7.4-gd php7.4-bcmath php7.4-curl
```

### PHP 8.0
```bash
sudo apt install php8.0-cli php8.0-fpm php8.0-xml php8.0-mbstring php8.0-curl php8.0-gd
```


## 使用不同版本的PHP

### 本地域名解析

使用命令 `sudo vim /etc/hosts` 修改 `hosts` 文件, 添加域名解析

```
127.0.0.1   73.php.cn
127.0.0.1   74.php.cn
```


### 安装 Nginx

```bash
sudo apt install nginx
```

### 权限配置

将  Nginx 和 PHP 权限配置为当前用户， 解决可能遇到的文件权限问题

使用命令 `whoami` 查看当前用户名, 比如: 当前用户为 `wyj`

```bash
➜  ~ whoami
wyj
```

使用命令 `sudo vim /etc/nginx/nginx.conf` 修改 Nginx 配置

```
user wyj;
```

使用命令 `sudo vim /etc/php/7.3/fpm/pool.d/www.conf` 修改 PHP7.3的配置

```
user = wyj
group = wyj

listen.owner = wyj
listen.group = wyj
```

使用命令 `sudo vim /etc/php/7.4/fpm/pool.d/www.conf` 修改 PHP7.4的配置

```
user = wyj
group = wyj

listen.owner = wyj
listen.group = wyj
```

权限修改完成之后， 重启 Nginx PHP7.3 PHP7.4 服务

```bash
sudo service nginx restart
sudo service php7.3-fpm restart
sudo service php7.4-fpm restart
```

### 创建项目

创建项目目录

```bash
mkdir ~/www/73.php.cn -p
mkdir ~/www/74.php.cn -p
```

分别在 `~/www/73.php.cn` 和 `~/www/74.php.cn` 目录下创建 `index.php` ， 文件内容如下

```php
<?php

echo phpinfo();
```

### Nginx 配置

使用命令 `sudo vim /etc/nginx/sites-available/73.php.cn.conf` 创建配置文件， 内容如下

```
server {
    listen 80;
    root /home/wyj/www/73.php.cn;
    index index.php index.html index.htm index.nginx-debian.html;
    server_name 73.php.cn;
    location / {
        try_files $uri $uri/ =404;
    }
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.3-fpm.sock;
    }
}
```

使用命令 `sudo vim /etc/nginx/sites-available/74.php.cn.conf` 创建配置文件， 内容如下

```
server {
    listen 80;
    root /home/wyj/www/74.php.cn;
    index index.php index.html index.htm index.nginx-debian.html;
    server_name 74.php.cn;
    location / {
        try_files $uri $uri/ =404;
    }
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
    }
}
```

创建软连接

```bash
sudo ln -s /etc/nginx/sites-available/73.php.cn.conf /etc/nginx/sites-enabled/73.php.cn.conf
sudo ln -s /etc/nginx/sites-available/74.php.cn.conf /etc/nginx/sites-enabled/74.php.cn.conf
```

### 测试

测试前重启所有的服务， 确保配置生效

```bash
sudo service nginx restart
sudo service php7.3-fpm restart
sudo service php7.4-fpm restart
```

访问 [http://73.php.cn](http://73.php.cn) 即可看到  `PHP Version 7.3.27-9+ubuntu20.04.1+deb.sury.org+1`

访问 [http://74.php.cn](http://74.php.cn) 即可看到  `PHP Version 7.4.15`


注： 火狐浏览器有 DNS 缓存

### 火狐关闭 DNS 缓存


火狐浏览器访问 [about:config](about:config)

搜索框搜索 `network.dnsCacheExpiration`

默认值是 `60` ， 修改为 `0` 即可
