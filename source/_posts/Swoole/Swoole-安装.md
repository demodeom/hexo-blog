---
title: Swoole - 安装
date: 2021-08-12 07:20:52
tags:
    - 安装
categories:
    - Swoole
---

Swoole 笔记

<!-- more -->

## 安装

[GitHub Swoole](https://hub.fastgit.org/swoole/swoole-src/releases)


Step1: 下载最新的源码, 解压

Step2: 切换到源码目录, 执行 `phpize`

Step3: 切换到源码目录, 执行 `./configure`

Step4: 切换到源码目录, 执行 `make`

Step5: 切换到源码目录, 执行 `sudo make install`

Step6: 修改 `php.ini` 文件, 文件尾部追加 `extension=swoole`

## Hello World

### 服务端

新建 `websocket_server_start.php`, 内容如下

```php
//创建WebSocket Server对象，监听0.0.0.0:9502端口
$ws = new Swoole\WebSocket\Server('0.0.0.0', 9502);

//监听WebSocket连接打开事件
$ws->on('Open', function ($ws, $request) {
    $ws->push($request->fd, "hello, welcome\n");
});

//监听WebSocket消息事件
$ws->on('Message', function ($ws, $frame) {
    echo "Message: {$frame->data}\n";
    $ws->push($frame->fd, "server: {$frame->data}");
});

//监听WebSocket连接关闭事件
$ws->on('Close', function ($ws, $fd) {
    echo "client-{$fd} is closed\n";
});

$ws->start();
```

使用命令 `php websocket_server_start.php` 启动服务


### 客户端(JS)

```js
//连接服务器
ws = new WebSocket("ws://192.168.75.130:9502");
ws.onopen = function(event){
    console.log(event);
};
ws.onmessage = function (event) {
    console.log(event);
    var msg = "<p>"+event.data+"</p>";
    $("#msgArea").append(msg);
}
ws.onclose = function(event){alert("已经与服务器断开连接\r\n当前连接状态："+this.readyState);};

ws.onerror = function(event){console.log("WebSocket异常！");};
```

## 多人聊天室


### 服务端

通过 `$server->connections` 获取所有websocket连接用户的fd，进而实现群发功能

```php
foreach ($server->connections as $fd) {
    // 需要先判断是否是正确的websocket连接，否则有可能会push失败
    if ($server->isEstablished($fd)) {
        $server->push($fd, $request->get['message']);
    }
}
```

客户端

```html

```
