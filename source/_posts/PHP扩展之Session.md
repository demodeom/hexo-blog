---
title: PHP扩展之Session
date: 2021-08-16 05:38:38
tags:
categories:
---


# session 配置



## 配置

约束： 如果配置后有相关函数， 说明配置支持通过函数动态设置

### name

`session.name string` session.name 指定会话名以用做 cookie 的名字。只能由字母数字组成，默认为 PHPSESSID;  参见 session_name()

请求响应头部可以看到类似一下信息

```
Set-Cookie: PHPSESSID=t3qhqf1159vn41hrem00r6v3b0; path=/
```

### auto

`session.auto_start bool` session.auto_start 指定会话模块是否在请求开始时自动启动一个会话。默认为 0（不启动）。 

只有在有需要的时候使用 `session_start()` 开启， 默认关闭，可以节约资源

### gc

session 垃圾回收是通过两个参数控制的

`session.gc_probability int` session.gc_probability 与 session.gc_divisor 合起来用来管理 gc（garbage collection 垃圾回收）进程启动的概率。默认为 1。详见 session.gc_divisor。 

`session.gc_divisor int` session.gc_divisor 与 session.gc_probability 合起来定义了在每个会话初始化时启动 gc（garbage collection 垃圾回收）进程的概率。此概率用 gc_probability/gc_divisor 计算得来。例如 1/100 意味着在每个请求中有 1% 的概率启动 gc 进程。session.gc_divisor 默认为 100。 

`session.gc_maxlifetime int`
    session.gc_maxlifetime 指定过了多少秒之后数据就会被视为“垃圾”并被清除。 垃圾搜集可能会在 session 启动的时候开始（ 取决于session.gc_probability 和 session.gc_divisor）。 


**笔记**

Session 由两部分组成， session_id 和 session_value ;

session_value存储在服务器， 访问 session_value 需要 session_id， 生命周期由服务端维护； 

session_id 存储在客户端的 cookie 中， 生命周期由客户端维护；

服务端 session 清理是概率性的， 可能存在延迟； 客户端 cookie 生命周期可以手动修改延长；因此在手动修改客户端cookie生命周期和session清理延迟的情况下， 出现 session 已经过期， 但是仍然有效的问题

如何解决： 

- session_value 记录 session 创建时间
- 访问 session 是， 通过当前事件和创建时间判断 session 是否过期

服务端通过概率事件的方式来启动GC， 可以提高服务性能

可以通过配置 gc_probability 和 gc_divisor 为相同的值， 来解决GC延迟的问题， 但是会降低性能


### cookie

` session.use_cookies bool` session.use_cookies 指定是否在客户端用 cookie 来存放会话 ID。默认为 1（启用）。 

`session.use_only_cookies bool` session.use_only_cookies 指定是否在客户端仅仅使用 cookie 来存放会话 ID。。启用此设定可以防止有关通过 URL 传递会话 ID 的攻击。默认值为1（启用） 

`session.cookie_lifetime int` session.cookie_lifetime 以秒数指定了发送到浏览器的 cookie 的生命周期。值为 0 表示“直到关闭浏览器”。默认为 0。参见 session_get_cookie_params() 和 session_set_cookie_params()。 

`session.cookie_path string` session.cookie_path 指定了要设定会话 cookie 的路径。默认为 /。参见 session_get_cookie_params() 和 session_set_cookie_params()。 

`session.cookie_domain string` session.cookie_domain 指定了要设定会话 cookie 的域名。默认为无，表示根据 cookie 规范产生 cookie 的主机名。参见 session_get_cookie_params() 和 session_set_cookie_params()。 


`session.cookie_secure bool` session.cookie_secure 指定是否仅通过安全连接发送 cookie。默认为 off。此设定是 PHP 4.0.4 添加的。参见 session_get_cookie_params() 和 session_set_cookie_params()。 


`session.cookie_httponly bool` 设置 cookie 仅能够通过 HTTP 协议访问。 它意味着 cookie 无法通过类似 JavaScript 这样的脚本语言访问。 该设置可有效避免 XSS 攻击窃取身份（但并非所有浏览器都支持）。 

`session.cache_limiter string` session.cache_limiter 指定会话页面所使用的缓冲控制方法（ none/nocache/ private/ private_no_expire/public）。默认为 nocache。参见 session_cache_limiter() 文档查看各个值的含义。 

`session.cache_expire int` session.cache_expire 以分钟数指定缓冲的会话页面的存活期，此设定对 nocache 缓冲控制方法无效。默认为 180。参见 session_cache_expire()。 


### 禁用 cookie 后 session 如何生效


`session.use_trans_sid bool` session.use_trans_sid 指定是否启用透明 SID 支持。默认为 0（禁用）。 

`session.trans_sid_tags string` 开启支持透明 sid 时， session.trans_sid_tags 指定是否需要重写 HTML 标签来包含 session id。 默认为 a=href,area=href,frame=src,input=src,form= form 是个特殊标签。 <input hidden="session_id" name="session_name"> 作为表单变量添加。 

`session.trans_sid_hosts string` 当透明 sid 支持开启时，session.trans_sid_hosts 设置了要重写包含 session 的主机名， 默认为 `$_SERVER['HTTP_HOST']`。 多个主机用半角逗号 "," 分隔；主机名之间不能用空格。 例如 php.net,wiki.php.net,bugs.php.net。 

`session.sid_length int` 通过 session.sid_length 可以设置 session ID 字符串长度。 Session ID 的长度可以是在 22 到 256 之间。 默认是 32。 如果需要较好的兼容性可以设置为 32、40 等。 较长的 session ID 会更难猜测。 推荐至少要 32 个字符。 

`session.sid_bits_per_character int` session.sid_per_character 设置要编码 session ID 字符的比特数。 可能的设置有： '4'（0-9、a-f）、'5'（0-9、a-v）、'6'（0-9、a-z、A-Z、"-"、","）。 默认为 4。 更多的比特数会产生更强健的 session ID。 对于绝大多数环境，推荐值是 5。 




面试可能会遇到这样的问题，如何解决呢？

以 a 标签为例， 将 cookie 添加到 a 标签 src 属性之后， 这样服务器就可以拿到 a 标签地址

`trans_sid_tags` 配置支持的标签以及标签对应的属性

**总结：** 虽然解决问题， 但是带来了安全问题； 比如： 可以通过脚本轻易获取 cookie

### upload


`session.upload_progress.enabled boolean ` 启用上传进度跟踪，填充 `$_SESSION` 变量。默认为 1，启用。

`session.upload_progress.cleanup boolean` 读取所有 POST 数据（即上传完成）后立即清除进度信息。默认为 1，启用。

`session.upload_progress.prefix string` 用于`$_SESSION`中的上传进度键的前缀。这个键将与`$_POST[ini_get("session.upload_progress.name")]`的值相连接，以提供一个唯一的索引。默认为 `upload_progress_`。

`session.upload_progress.name string` 在`$_SESSION`中用于存储进度信息的键的名称。参见`session.upload_progress.prefix`。如果`$_POST[ini_get("session.upload_progress.name")]`没有通过或可用，上传进度将不会被记录。默认为 `"PHP_SESSION_UPLOAD_PROGRESS"`。

`session.upload_progress.freq mixed` 定义上传进度信息的更新频率。这可以以字节为单位（即 "每100字节后更新进度信息"），或以百分比为单位（即 "每收到整个文件大小的1%后更新进度信息"）。默认为 "1%"。

`session.upload_progress.min_freq` int 更新之间的最小延迟，以秒为单位。默认为“1”（一秒）。

除非`session.upload_progress.enabled`被启用，并且`$_POST[ini_get("session.upload_progress.name")]`变量被设置，否则上传进度将不会被注册。关于此功能的细节，请参见会话上传进度。

 当 session.upload_progress.enabled INI 选项开启时，PHP 能够在每一个文件上传时监测上传进度。 这个信息对上传请求自身并没有什么帮助，但在文件上传时应用可以发送一个POST请求到终端（例如通过XHR）来检查这个状态

当一个上传在处理中，同时POST一个与INI中设置的`session.upload_progress.name`同名变量时，上传进度可以在`$_SESSION`中获得。 当PHP检测到这种POST请求时，它会在`$_SESSION`中添加一组数据, 索引是 `session.upload_progress.prefix `与 `session.upload_progress.name`连接在一起的值。 通常这些键值可以通过读取INI设置来获得，例如 

```php
$key = ini_get("session.upload_progress.prefix") . ini_get("session.upload_progress.name");
var_dump($_SESSION[$key]);
```

通过将`$_SESSION[$key]["cancel_upload"]`设置为`true`，还可以取消一个正在处理中的文件上传。 当在同一个请求中上传多个文件，它仅会取消当前正在处理的文件上传和未处理的文件上传，但是不会移除那些已经完成的上传。 当一个上传请求被这么取消时，`$_FILES`中的error将会被设置为 `UPLOAD_ERR_EXTENSION`。

`session.upload_progress.freq `和 `session.upload_progress.min_freq` INI选项控制了上传进度信息应该多久被重新计算一次。 通过合理设置这两个选项的值，这个功能的开销几乎可以忽略不计。 

### lazy_write

`session.lazy_write bool` session.lazy_write，设置成 1 的含义是： 只有 session 数据发生变化时才需要重新写入。默认为开启状态 1。 



 session.serialize_handler string
    session.serialize_handler 定义用来序列化／反序列化的处理器名字。 当前支持 PHP 序列化格式 (名为 php_serialize)、 PHP PHP 内部格式 (名为 php 及 php_binary) 和 WDDX (名为 wddx)。 如果 PHP 编译时加入了 WDDX 支持，则只能用 WDDX。 php_serialize 在内部简单地直接使用 serialize/unserialize 函数，并且不会有 php 和 php_binary 所具有的限制。 使用较旧的序列化处理器导致 $_SESSION 的索引既不能是数字也不能包含特殊字符(| and !) 。 使用 php_serialize 避免脚本退出时，数字及特殊字符索引导致出错。 默认使用 php。 


## 函数


