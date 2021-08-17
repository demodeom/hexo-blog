---
title: Redis - Server配置
date: 2021-08-12 07:00:31
tags:
    - Redis
    - RedisServer
categories:
    - Redis
---

Redis Server 配置文件 `redis.conf` 翻译

<!-- more -->

## 单位换算

```
# 1k => 1000 bytes
# 1kb => 1024 bytes
# 1m => 1000000 bytes
# 1mb => 1024*1024 bytes
# 1g => 1000000000 bytes
# 1gb => 1024*1024*1024 bytes

# units are case insensitive so 1GB 1Gb 1gB are all the same.
```


## 基础

```
bind 127.0.0.1 -::1
```

默认情况下，保护模式是启用的。只有当你确定你想让其他主机的客户端连接到Redis，即使没有配置认证，也没有使用 "bind "指令明确列出一组特定的接口时，你才应该禁用它。

```
protected-mode yes
```

接受指定端口的连接，默认为6379。

```
port 6379
```

在每秒高请求的环境中，你需要一个高的积压，以避免缓慢的客户连接问题。注意，Linux内核会默默地将其截断为/proc/sys/net/core/somaxconn的值，所以要确保同时提高somaxconn和tcp_max_syn_backlog的值，以获得预期的效果。

```
tcp-backlog 511
```

指定Unix套接字的路径，该套接字将用于监听进入的连接。没有默认值，所以Redis不会在未指定时监听unix套接字。

```
# unixsocket /run/redis.sock
# unixsocketperm 700
```

在客户闲置N秒后关闭连接（0到禁用）。

```
timeout 0
```

TCP keepalive. 秒

```
tcp-keepalive 300
```

默认情况下，Redis不作为一个守护程序运行。如果你需要，请使用'yes'。 注意，Redis在守护进程中会在/var/run/redis.pid中写入一个pid文件。 当Redis由upstart或systemd监管时，这个参数没有影响。

```
daemonize yes
```

如果你从upstart或systemd运行Redis，Redis可以与你的监督树互动。选项。

- supervised no      - 没有监督互动
- supervised upstart - 通过将Redis置入SIGSTOP模式来发出启动信号，要求在你的启动工作配置中使用 "期望停止"。
- supervised systemd - 通过在启动时向 `$NOTIFY_SOCKET` 写入 `READY=1`，向systemd发出信号，并定期更新Redis状态。
- supervised auto    - 根据 `PSTART_JOB` 或 `NOTIFY_SOCKET` 环境变量检测 `upstart` 或 `systemd` 方法

```
# supervised auto
```

如果指定了一个pid文件，Redis会在启动时将其写入指定位置，并在退出时将其删除。

当服务器在非守护状态下运行时，如果配置中没有指定pid文件，则不会创建pid文件。当服务器处于守护状态时，即使没有指定，也会使用pid文件，默认为"/var/run/redis.pid"。

创建pid文件是最好的努力：如果Redis不能创建它，不会发生什么坏事，服务器会正常启动和运行。

请注意，在现代Linux系统上，"/run/redis.pid "更符合要求，应该用它来代替。

```
pidfile /var/run/redis_6379.pid
```

指定服务器的粗暴程度。这可以是以下之一。

- debug
- verbose
- notice
- warning

```
loglevel notice
```

指定日志文件的名称。也可以用空字符串来强制Redis在标准输出上记录。注意，如果你使用标准输出进行日志记录，但又进行daemonize，日志将被发送到/dev/null

```
logfile ""
```

要启用对系统日志的记录，只需将'syslog-enabled'设置为yes，并可选择更新其他syslog参数以满足你的需要。

```
# syslog-enabled no
```

指定syslog身份。

```
# syslog-ident redis
```

指定syslog设施。必须是USER或在LOCAL0-LOCAL7之间。

```
# syslog-facility local0
```

要禁用内置的崩溃日志，这可能会在需要时产生更干净的核心转储，请取消以下内容。

```
# crash-log-enabled no
```

要禁用作为崩溃日志一部分运行的快速内存检查，这可能会让 redis 更快地终止，请取消以下内容。

```
# crash-memcheck-enabled no
```

设置数据库的数量。默认的数据库是DB 0，你可以使用`SELECT <dbid>`在每个连接的基础上选择一个不同的数据库，其中dbid是0和'databases'-1之间的数字。

```
databases 16
```

默认情况下，Redis只在启动记录到标准输出时显示ASCII艺术标志，如果标准输出是TTY，并且禁用syslog记录。基本上这意味着通常只有在交互式会话中才会显示一个标志。

然而，可以通过将以下选项设置为 "是 "来强制执行4.0之前的行为，并在启动日志中始终显示ASCII艺术标志。

```
always-show-logo no
```

默认情况下，Redis修改了进程标题（如在'top'和'ps'中看到的），以提供一些运行时信息。可以通过将以下内容设置为 "否 "来禁用这一点，并使进程名称保持执行状态。

```
set-proc-title yes
```

当改变进程标题时，Redis使用以下模板来构建修改后的标题。

模板变量是在大括号中指定的。以下的变量是


-  `{title}`           如果是父进程，则执行的进程名称，或子进程的类型。
-  `{listen-addr}`     绑定地址或'`*`'，后面跟着监听的TCP或TLS端口，如果只有Unix套接字，也可以用这个端口。
-  `{server-mode}`     特殊模式, i.e. sentinel or cluster
-  `{port}`            监听的TCP端口为, or 0.
-  `{tls-port}`        监听的TLS端口为, or 0.
-  `{unixsocket}`      监听Unix域套接字的, or "".
-  `{config-file}`     使用的配置文件的名称.

```
proc-title-template "{title} {listen-addr} {server-mode}"
```

工作目录, DB将被写入这个目录中，文件名是用'dbfilename'配置指令指定的。 Append Only文件也将在这个目录下创建。 注意，你必须在这里指定一个目录，而不是一个文件名。

```
dir ./
```

## 数据持久化 RDB

在3600秒（一小时）后，如果至少有一个键被改变了; 一下三个条件满足其一即可

```
# save 3600 1
# save 300 100
# save 60 10000
```

默认情况下，如果启用了RDB快照（至少有一个保存点），并且最近一次后台保存失败，Redis将停止接受写操作。这将使用户意识到（以一种艰难的方式）数据没有正确地持久化在磁盘上，否则有可能没有人注意到，一些灾难会发生。

如果后台保存过程将再次开始工作，Redis将自动允许再次写入。

然而，如果你已经设置了对Redis服务器和持久性的适当监控，你可能想禁用这个功能，这样即使磁盘、权限等出现问题，Redis也会继续照常工作。

```
stop-writes-on-bgsave-error yes
```

转储`.rdb`数据库时使用LZF压缩字符串对象？ 默认情况下，压缩是启用的，因为它几乎总是一个胜利。 如果你想在保存过程中节省一些CPU，把它设置为 "NO"，但如果你有可压缩的值或键，数据集可能会更大。

```
rdbcompression yes
```

从RDB的第5个版本开始，CRC64校验码被放在文件的最后。 这使得该格式对损坏的抵抗力更强，但在保存和加载RDB文件时要付出性能上的代价（大约10%），所以你可以禁用它以获得最大的性能。

```
rdbchecksum yes
```

在禁用校验和的情况下创建的RDB文件的校验和为零，这将告诉加载代码跳过检查。

```
rdbchecksum yes
```

在加载RDB或RESTORE有效载荷时，启用或禁用对ziplist和listpack等的全面卫生检查。这可以减少以后在处理命令时出现断言或崩溃的机会。

- no         - 绝不要进行全面的卫生检查
- yes        - 始终进行全面的卫生检查
- clients    - 仅对用户连接进行全面卫生检查。不包括。RDB文件，从主连接收到的RESTORE命令，以及有skip-sanitize-payload ACL标志的客户连接。

默认应该是 "客户"，但由于它目前会影响到通过MIGRATE进行的集群重分，所以默认情况下暂时设置为 "不"。

```
# sanitize-dump-payload no
```

转储数据库的文件名

```
dbfilename dump.rdb
```

在没有启用持久性的实例中，移除复制使用的RDB文件。默认情况下，这个选项是禁用的，但是在某些环境下，出于法规或其他安全考虑，主站在磁盘上持久化的RDB文件，以便为复制站提供信息，或者复制站存储在磁盘上，以便为初始同步加载它们，应该尽快删除。注意，这个选项只适用于同时禁用AOF和RDB持久化的实例，否则将被完全忽略。

获得同样效果的另一种方法（有时更好）是在主实例和复制实例上使用无盘复制。然而，在复制的情况下，无盘并不总是一种选择。

```
rdb-del-sync-files no
```

## 数据持久化 AOF

默认情况下，Redis异步地将数据集转储到磁盘上。这种模式在许多应用中已经很好了，但Redis进程的问题或断电可能会导致几分钟的写入量损失（取决于配置的保存点）。

Append Only File是一种替代的持久化模式，提供了更好的持久性。例如，使用默认的数据sync策略（见后面的配置文件），Redis在服务器断电这样的戏剧性事件中可能只损失一秒钟的写入量，或者如果Redis进程本身出现问题，但操作系统仍在正常运行，则只损失一次写入。

AOF和RDB持久化可以同时启用，不会有问题。如果在启动时启用AOF，Redis将加载AOF，那是具有更好持久性保证的文件。

请查看https://redis.io/topics/persistence，了解更多信息。


```
appendonly no
```
appendonly no

只附加文件的名称（默认："appendonly.aof"）。

```
appendfilename "appendonly.aof"
```

fsync()调用告诉操作系统实际写数据到磁盘上，而不是等待输出缓冲区中的更多数据。有些操作系统会真的把数据冲到磁盘上，另一些操作系统只是试图尽快完成它。

Redis支持三种不同的模式。

- no：不进行fsync，只是让操作系统在它想的时候刷新数据。更快。
- always：在每次写完append only log后进行fsync。慢，最安全。
- everysec：每秒钟只进行一次fsync。妥协。默认是 "everysec"，因为这通常是速度和数据安全之间的正确妥协。这取决于你是否能把它放宽到 "no"，让操作系统在它想的时候刷新输出缓冲区，以获得更好的性能（但如果你能接受一些数据丢失的想法，可以考虑默认的持久化模式，即快照），或者相反，使用 "always"，这非常慢，但比everysec更安全。



更多细节请查看以下文章。 http://antirez.com/post/redis-persistence-demystified.html

如果不确定，请使用 "everysec"。
```
# appendfsync always
appendfsync everysec
# appendfsync no
```

当AOF fsync策略被设置为总是或每秒钟，并且后台保存进程（后台保存或AOF日志后台重写）正在对磁盘执行大量的I/O，在一些Linux配置中，Redis可能在fsync()调用中阻塞太长时间。请注意，目前还没有解决这个问题，因为即使在不同的线程中执行fsync也会阻塞我们的同步write(2)调用。

为了缓解这个问题，可以使用以下选项，当BGSAVE或BGREWRITEAOF正在进行时，它将阻止主进程调用fsync（）。

这意味着，当另一个子程序正在保存时，Redis的耐久性与 "appendfsync none "相同。在实际操作中，这意味着在最坏的情况下，有可能损失多达30秒的日志（在默认的Linux设置下）。

如果你有延迟的问题，把它改为 "是"。否则就把它设为 "no"，从持久性的角度来看，这是最安全的选择。

```
no-appendfsync-on-rewrite no
```

自动重写仅有的append文件。当AOF日志大小增长到指定的百分比时，Redis能够自动重写日志文件，隐式调用BGREWRITEAOF。

它是这样工作的。Redis会记住最近一次重写后AOF文件的大小（如果重启后没有发生重写，则使用启动时AOF的大小）。

这个基础大小与当前大小进行比较。如果当前的大小大于指定的百分比，就会触发重写。另外，你还需要为要重写的AOF文件指定一个最小尺寸，这对避免重写AOF文件很有用，即使达到了增加的百分比，但它仍然相当小。

指定一个零的百分比，以便禁用自动AOF重写功能。

```
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb
```

在Redis启动过程中，当AOF数据被加载回内存时，可能会发现AOF文件在末端被截断了。这可能发生在Redis运行的系统崩溃时，特别是当ext4文件系统被挂载而没有data=ordered选项时（然而这不会发生在Redis本身崩溃或中止但操作系统仍然正常工作时）。

当这种情况发生时，Redis可以以错误退出，或者尽可能多地加载数据（现在的默认情况），如果发现AOF文件在末尾被截断，则启动。下面的选项可以控制这种行为。

如果 aof-load-truncated 被设置为 yes，则会加载一个被截断的 AOF 文件，并且 Redis 服务器开始发出一个日志以告知用户该事件。否则，如果该选项被设置为 "否"，服务器会以错误的方式中止，并拒绝启动。当该选项被设置为 "否 "时，用户需要在重启服务器之前使用 "redis-check-aof "工具修复AOF文件。

注意，如果AOF文件在中间被发现损坏，服务器仍然会以错误退出。这个选项只适用于Redis试图从AOF文件中读取更多数据，但没有找到足够的字节。

```
aof-load-truncated yes
```

当重写AOF文件时，Redis能够在AOF文件中使用RDB序言，以加快重写和恢复速度。当这个选项被打开时，重写的AOF文件是由两个不同的句子组成。

` [RDB file][AOF tail]`

在加载时，Redis识别出AOF文件以 "REDIS "字符串开始，并加载前缀的RDB文件，然后继续加载AOF尾部。

```
aof-use-rdb-preamble yes
```


## 主从配置

### 从机配置

Master 服务IP 端口

```
#replicaof <masterip> <masterport>
```

如果 Master 设置了 `requirepass`

```
# masterauth <master-password>
```

如果 Master 配置了 ACLs

```
# masteruser <username>
```

当指定masteruser时，副本将使用新的AUTH形式对其master进行认证。 `AUTH <username> <password>`。

当副本失去与主站的连接时，或者当复制仍在进行时，副本可以以两种不同的方式行动。

- 如果replica-serve-stale-data被设置为'yes'（默认），复制体仍然会回复客户的请求，可能会有过时的数据，或者如果这是第一次同步，数据集可能只是空的。
- 如果replica-serve-stale-data被设置为 "no"，复制体将对所有命令回复错误 "与主站同步中"，除了。INFO, REPLICAOF, AUTH, PING, SHUTDOWN, REPLCONF, ROLE, CONFIG, SUBSCRIBE, UNSUBSCRIBE, PSUBSCRIBE, PUBLISH, PUBSUB, COMMAND, POST, HOST 和 LATENCY。

```
replica-serve-stale-data yes
```

从服务可写是危险的， 所以设置为只读

```
replica-read-only yes
```

复制同步策略：disk 或 socket

新的复制体和重新连接的复制体如果不能继续复制过程，只是接收差异，需要做所谓的 "完全同步"。一个RDB文件从主站被传送到复制站。

传递可以以两种不同的方式发生。

- Disk-backed Redis主进程创建一个新的进程，将RDB文件写入磁盘。之后，该文件由父进程递增地转移到副本中。
- Diskless Redis主站创建一个新进程，直接将RDB文件写入复制套接字，完全不接触磁盘。

在有  disk-backed 复制中，在生成RDB文件的同时，更多的复制可以被排队，并在当前生成RDB文件的子代完成工作后立即提供RDB文件。而用无盘复制，一旦传输开始，新到达的复制将被排队，当当前的传输终止时，新的传输将开始。

当使用 diskless 复制时，主站在开始传输前会等待一段可配置的时间（以秒为单位），希望多个复制会到达，传输可以并行化。

对于慢速磁盘和快速（大带宽）网络，无盘复制的效果更好。

```
repl-diskless-sync no
```

当启用无盘复制时，可以配置服务器等待的延迟，以便催生通过套接字将RDB传输到复制体的子程序。

这一点很重要，因为一旦传输开始，就不可能为到达的新副本提供服务，这些副本将被排在下一次RDB传输的队列中，所以服务器要等待一个延迟，以便让更多的副本到达。

延迟的单位是秒，默认是5秒。要完全禁用它，只需将其设置为0秒，传输将尽快开始。

```
repl-diskless-sync-delay 5
```

警告：RDB无磁盘负载是试验性的。因为在这种设置中，副本不会立即在磁盘上存储RDB，所以在故障恢复期间可能会导致数据丢失。RDB无盘加载+Redis模块不处理I/O读取也可能导致Redis在与主站的初始同步阶段出现I/O错误时中止。只有在你知道自己在做什么的情况下才使用。


Replica可以直接从套接字中加载它从复制链接中读取的RDB，或者将RDB存储到一个文件中，在完全接收到主站的RDB后再读取该文件。

在很多情况下，磁盘比网络慢，存储和加载RDB文件可能会增加复制时间（甚至会增加主站对Write内存和salve缓冲区的Copy）。然而，直接从套接字中解析RDB文件可能意味着在收到完整的rdb之前，我们必须刷新当前数据库的内容。出于这个原因，我们有以下选项。

- "disabled"    - 不要使用无盘加载（先将rdb文件存储到磁盘）。
- "on-empty-db" - 只有在完全安全的情况下才使用无盘负载。
- "swapdb"      - 在RAM中保留一份当前数据库内容的副本，同时直接从套接字中解析数据。注意，这需要足够的内存，如果你没有内存，你就有可能被OOM杀死。

```
repl-diskless-load disabled
```

复制体以预定的时间间隔向服务器发送PING信号。可以用 repl_ping_replica_period 选项来改变这个间隔。默认值是10秒。

```
# repl-ping-replica-period 10
```

下面的选项设置了复制的超时时间。


- 1) 从副本的角度来看，在同步过程中批量传输I/O。
- 2) 从复制体的角度看，主站超时（数据、ping）。
- 3) 从主站的角度看复制的超时（REPLCONF ACK pings）。

重要的是要确保这个值大于repl-ping-replica-period指定的值，否则每次主站和副本之间的流量低时都会检测到超时。默认值是60秒。

```
# repl-timeout 60
```

在SYNC之后禁用复制套接字的TCP_NODELAY？

如果你选择 "是"，Redis将使用较少的TCP数据包和较少的带宽来发送数据给副本。但这可能会增加数据出现在副本上的延迟，在使用默认配置的Linux内核中，延迟可达40毫秒。

如果你选择 "否"，数据出现在副本上的延迟将减少，但更多的带宽将被用于复制。

默认情况下，我们对低延迟进行了优化，但在流量非常大的情况下，或者当主站和复制站相距很多跳时，将其改为 "是 "可能是一个好主意。

```
repl-disable-tcp-nodelay no
```

设置复制积压的大小。积压是一个缓冲区，当复制被断开一段时间后，会积累复制数据，这样当复制想再次重新连接时，往往不需要完全重新同步，而只需要部分重新同步就可以了，只是传递复制在断开连接时错过的那部分数据。

复制积压越大，复制体可以忍受断开连接的时间越长，以后就可以进行部分重新同步。 只有在至少有一个副本连接的情况下才会分配积压。

```
# repl-backlog-size 1mb
```

当主站在一段时间内没有连接副本后，积压的数据将被释放。下面的选项配置了从最后一个副本断开连接开始，需要经过多少秒才能释放积压的缓冲区。

请注意，副本永远不会因为超时而释放积压，因为它们以后可能会被提升为主站，并且应该能够正确地与其他副本进行 "部分再同步"：因此它们应该始终积累积压。

值为0意味着从不释放积压。

```
# repl-backlog-ttl 3600
```

复制的优先级是一个整数，由Redis在INFO输出中公布。它被Redis Sentinel用来选择一个副本，以便在主副本不再正常工作时将其提升为主副本。

优先级低的副本被认为更适合推广，例如，如果有三个优先级为10、100、25的副本，Sentinel会选择优先级为10的副本，也就是最低的那个。

然而，一个特殊的优先级为0的副本标志着它不能执行主站的角色，所以优先级为0的副本将永远不会被Redis Sentinel选中进行推广。
默认情况下，优先级是100。

```
replica-priority 100
```

默认情况下，Redis Sentinel 在其报告中包括所有副本。一个副本可以从 Redis Sentinel 的公告中排除。一个未公布的副本将被`'sentinel replicas <master>'`命令忽略，并且不会暴露给 Redis Sentinel 的客户端。

这个选项不会改变副本优先级的行为。即使将 replica-announced 设置为 'no'，副本也可以被提升为主副本。要防止这种行为，请将 replica-priority 设为 0。

```
# replica-announced yes
```

如果连接的副本少于N个，滞后时间小于或等于M秒，主站就有可能停止接受写入。

这N个复制体需要处于 "在线 "状态。

以秒为单位的滞后，必须<=指定的值，从副本收到的最后一次ping中计算出来，通常每秒钟发送一次。

这个选项并不保证N个副本会接受写入，但会限制在没有足够的副本可用的情况下丢失写入的窗口，以指定的秒数为准。

将其中一个设置为0，就可以禁用该功能。 默认情况下，min-replicas-to-write被设置为0（功能禁用），min-replicas-max-lag被设置为10。

例如，要求至少有3个副本，滞后时间<=10秒，使用。

```
# min-replicas-to-write 3
# min-replicas-max-lag 10
```

Redis主站能够以不同方式列出所附复制的地址和端口。例如，"INFO复制 "部分提供了这些信息，除其他工具外，Redis Sentinel使用这些信息以发现复制实例。另一个提供这种信息的地方是主站的 "ROLE "命令的输出。

通常由复制体报告的列出的IP地址和端口是通过以下方式获得的。

- IP：该地址是通过检查复制体用来与主站连接的套接字的对等地址自动检测的。
- 端口：端口由复制体在复制握手期间传达，通常是复制体用于监听连接的端口。

然而，当使用端口转发或网络地址转换（NAT）时，复制体实际上可以通过不同的 IP 和端口对进行访问。复制体可以使用下面两个选项，以便向其主站报告一组特定的IP和端口，这样INFO和ROLLE就会报告这些值。

如果你只需要覆盖端口或IP地址，就没有必要同时使用这两个选项。

```
# replica-announce-ip 5.5.5.5
# replica-announce-port 1234
```

## KEYS TRACKING

Redis实现了对客户端缓存值的服务器辅助支持。这是用一个无效表实现的，该表使用一个以键名为索引的弧度键，记住了哪些客户拥有哪些键。反过来，这被用来向客户端发送无效信息。请查看此页面以了解更多关于该功能的信息。

[ https://redis.io/topics/client-side-caching]( https://redis.io/topics/client-side-caching)

当客户端的跟踪功能被启用时，所有的只读查询都被认为是被缓存的：这将迫使Redis在失效表中存储信息。当键被修改时，这些信息会被冲走，无效信息会被发送到客户端。然而，如果工作负载主要是读取，Redis可能会使用越来越多的内存，以跟踪许多客户端获取的键。

出于这个原因，我们可以为无效表配置一个最大的填充值。默认情况下，它被设置为1M的键，一旦达到这个限制，Redis将开始驱逐无效表中的键，即使它们没有被修改过，只是为了回收内存：这将反过来迫使客户端失效缓存的值。基本上，表的最大尺寸是在你想花费服务器端的内存来跟踪谁缓存了什么的信息，和客户端在内存中保留缓存对象的能力之间的权衡。

如果你把这个值设置为0，就意味着没有任何限制，Redis会根据需要在失效表中保留多少个键。 在 "统计 "INFO部分，你可以找到每个特定时刻无效表中的键的数量信息。

注意：当在广播模式下使用钥匙跟踪时，服务器端不使用任何内存，所以这个设置是无用的。

```
# tracking-table-max-keys 1000000
```

## SECURITY

警告：由于Redis的速度相当快，一个外部用户对一个现代化的盒子每秒可以尝试多达100万个密码。这意味着你应该使用非常强大的密码，否则将非常容易被破解。请注意，因为密码实际上是客户端和服务器之间的共享秘密，不应该被任何人类记住，密码可以很容易地从/dev/urandom或其他地方得到一个长字符串，所以通过使用一个长的和不可猜测的密码，任何暴力攻击将是可能的。

Redis ACL用户的定义格式如下。

```
user <username> ... acl rules ...
```

For example:

```
user worker +@list +@connection ~jobs:* on >ffa9203c493aa99
```

特殊的用户名 "default "被用于新的连接。如果这个用户有 "nopass "规则，那么新连接将立即被认证为 "default "用户，而不需要通过AUTH命令提供任何密码。否则，如果 "default "用户没有标记为 "nopass"，那么连接将以未认证状态开始，并需要AUTH（或HELLO命令的AUTH选项）才能被认证并开始工作。

描述一个用户可以做什么的ACL规则如下。

- `on` 启用该用户：可以以该用户身份进行认证。
- `off` 禁用该用户：不再可能以该用户身份进行认证，但已认证的连接仍能工作。
- `skip-sanitize-payload` RESTORE dump-payload sanitation被跳过。
- `sanitize-payload` RESTORE dump-payload是经过消毒的（默认）。
- `+<command>` 允许执行该命令
- `-<command>` 不允许执行该命令。
- `+@<category>` 允许执行该类别中的所有命令，有效的类别包括` @admin`, `@set`, `@sortedset`, ...等等，请参阅server.c文件中的完整列表，其中描述和定义了Redis命令表。特殊类别 `@all` 表示所有的命令，但目前存在于服务器中，将来会通过模块加载。
- `+<command>|subcommand` 允许一个原本禁用的命令的特定子命令。注意，这种形式不允许像-DEBUG|SEGFAULT那样是负数，而只允许以 "+"开头的加法。
- `allcommands` `+@all`的别名。注意，它意味着能够执行所有通过模块系统加载的未来命令。
- `nocommands` 是 -@all 的别名。
- `~<pattern>` 添加一个可以作为命令的一部分被提及的键的模式。例如，`~*`允许所有的键。该模式是一个类似于KEYS的glob风格的模式。可以指定多个模式。
- `allkeys ~*`的别名
- `resetkeys` 刷新允许的按键模式列表。
- `&<pattern>` 添加一个用户可以访问的Pub/Sub通道的glob风格模式。可以指定多个通道模式。
- `allchannels` 是 `&*` 的别名。
- `resetchannels` 冲洗允许的频道模式的列表。
- `><password>` 把这个密码添加到用户的有效密码列表中。例如，>mypass 将添加 "mypass "到列表中。这条指令清除了 "nopass "标志（见下文）。
- `<<password>` 从有效密码列表中删除此密码。
- `nopass` 该用户的所有设置的密码都被删除，并且该用户被标记为不需要密码：这意味着每个密码都会对该用户有效。如果这个指令被用于默认用户，每一个新的连接将被立即认证为默认用户而不需要任何明确的AUTH命令。请注意，"resetpass "指令将清除这个条件。
- `resetpass` 冲洗允许的密码列表。此外，还将删除 "nopass "状态。在 "resetpass "之后，用户没有相关的密码，如果不添加一些密码（或者以后设置为 "nopass"），就没有办法进行认证。
- `reset` 执行以下操作：`resetpass`, `resetkeys`, `off`, `-@all`。用户返回到其创建后立即具有的相同状态。

ACL规则可以以任何顺序指定：例如，你可以从密码开始，然后是标志，或钥匙模式。但是要注意的是，加法和减法规则的含义会根据顺序的不同而改变。例如，请看下面的例子。

```
#   user alice on +@all -DEBUG ~* >somepassword
```

This will allow "alice" to use all the commands with the exception of the DEBUG command, since +@all added all the commands to the set of the commands alice can use, and later DEBUG was removed. However if we invert the order of two ACL rules the result will be different:

```
#   user alice on -DEBUG +@all ~* >somepassword
```

现在，当alice在允许的命令集合中还没有命令时，DEBUG就被删除了，后来所有的命令都被加入了，所以用户将能够执行所有的命令。

基本上ACL规则是从左到右处理的。
关于ACL配置的更多信息，请参考Redis的网站：https://redis.io/topics/acl
ACL日志
ACL日志跟踪与ACL相关的失败命令和认证事件。ACL日志对于解决被ACL阻止的失败命令很有用。ACL日志被存储在内存中。你可以用ACL LOG RESET来回收内存。定义ACL日志的最大条目长度如下。

```
acllog-max-len 128
```

使用外部ACL文件

不在此文件中配置用户，可以使用一个独立的文件，只列出用户。这两种方法不能混用：如果你在这里配置用户，同时激活外部ACL文件，服务器将拒绝启动。

外部 ACL 用户文件的格式与 redis.conf 中用于描述用户的格式完全相同。

```
# aclfile /etc/redis/users.acl
```

重要提示：从Redis 6开始，"requirepass "只是新ACL系统之上的一个兼容层。该选项的作用只是为默认用户设置密码。

客户端仍将像往常一样使用`AUTH <password>`进行认证，或者更明确地使用`AUTH default <password>`，如果他们遵循新的协议：两者都可以工作。

```
# requirepass foobared
```

默认情况下，新用户是以限制性权限初始化的，通过相当于这个ACL规则 "off resetkeys -@all"。从Redis 6.2开始，也可以用ACL规则管理对Pub/Sub通道的访问。如果是新用户，默认的Pub/Sub通道权限是由acl-pubsub-default配置指令控制的，它接受以下值之一。


```  
# allchannels: grants access to all Pub/Sub channels
# resetchannels: revokes access to all Pub/Sub channels
```

为了确保升级Redis 6.0时的向后兼容性，acl-pubsub-default默认为'allchannels'权限。

未来的兼容性说明：在未来的Redis版本中，为了提供更好的开箱即用的Pub/Sub安全，该指令的默认值'allchannels'很可能会被改为'resetchannels'。

因此，建议你为所有用户明确定义Pub/Sub权限，而不是依赖隐式默认值。一旦你为所有现有的用户设置了明确的Pub/Sub，你应该取消对下面一行的注释。

```
# acl-pubsub-default resetchannels
```

## CLIENTS

设置同时连接的客户端的最大数量。默认情况下，这个限制被设置为10000个客户端，然而如果Redis服务器无法配置进程文件限制以允许指定的限制，允许的最大客户端数量被设置为当前文件限制减去32（因为Redis保留了一些文件描述符供内部使用）。

一旦达到限制，Redis将关闭所有的新连接，并发送一个错误 "达到最大客户数"。

重要的是：当使用Redis集群时，最大连接数也与集群总线共享：集群中的每个节点将使用两个连接，一个传入，一个传出。在集群非常大的情况下，相应地确定限制的大小是很重要的。

```
# maxclients 10000
```

## 内存管理

设置一个内存使用限制到指定的字节数。当达到内存限制时，Redis将尝试根据所选择的驱逐策略（见maxmemory-policy）来删除键。

如果Redis不能根据策略删除键，或者策略被设置为 "noeviction"，Redis将开始对会使用更多内存的命令做出错误回复，比如SET、LPUSH等，并将继续回复GET等只读命令。

这个选项通常在使用Redis作为LRU或LFU缓存时很有用，或者为一个实例设置一个硬的内存限制（使用'noeviction'策略）。

警告：如果你在一个实例上附加了maxmemory的副本，那么需要为副本提供的输出缓冲区的大小将从使用的内存数中减去，这样网络问题/重新同步将不会触发一个循环，即键被驱逐，反过来，副本的输出缓冲区被驱逐的键的DEL充满，触发更多的键的删除，以此类推直到数据库被完全清空。

简而言之......如果你有复制品，建议你为maxmemory设置一个下限，以便系统上有一些空闲的RAM用于复制品输出缓冲区（但如果策略是 "noeviction "就不需要）。


```
# maxmemory <bytes>
```

MAXMEMORY政策：当达到maxmemory时，Redis将如何选择要删除的内容。你可以从以下行为中选择一个。

- volatile-lru -> 使用近似的LRU进行驱逐，只驱逐有过期集的钥匙。
- allkeys-lru -> 使用近似的LRU来驱逐任何密钥。
- volatile-lfu -> 使用近似的LFU进行驱逐，只驱逐有过期集的钥匙。
- allkeys-lfu -> 使用近似的LFU驱逐任何钥匙。
- volatile-random -> 删除一个有过期设置的随机密钥。
- allkeys-random -> 删除一个随机密钥，任何密钥。
- volatile-ttl -> 移除过期时间最近的密钥（小TTL）。
- noeviction -> 不要驱逐任何东西，只是在写操作时返回一个错误。

LRU指的是最近使用最少的。
LFU是指最不经常使用

LRU、LFU和volatile-ttl都是使用近似的随机化算法实现的。

注意：在上述任何一种策略下，当没有合适的键进行驱逐时，Redis会在需要更多内存的写操作上返回一个错误。这些通常是创建新键、添加数据或修改现有键的命令。几个例子是。SET、INCR、HSET、LPUSH、SUNIONSTORE、SORT（由于STORE参数）和EXEC（如果事务包括任何需要内存的命令）

```
# maxmemory-policy noeviction
```

LRU、LFU和最小TTL算法不是精确的算法，而是近似的算法（为了节省内存），所以你可以根据速度或准确性来调整它。默认情况下，Redis将检查5个键，并挑选最近使用最少的一个，你可以使用以下配置指令改变样本大小。

默认的5产生足够好的结果。10非常接近真正的LRU，但要花费更多的CPU。3比较快，但不是很准确。


```
# maxmemory-samples 5
```

驱逐处理被设计为在默认设置下运行良好。 如果有异常大的写入流量，这个值可能需要增加。 降低这个值可能会减少延迟，但会有驱逐处理的风险 0=最小延迟，10=默认，100=不考虑延迟的处理

```
# maxmemory-eviction-tenacity 10
```

从Redis 5开始，默认情况下，副本将忽略它的最大内存设置（除非它在故障切换后被提升到主站或手动）。这意味着键的驱逐将由主站处理，在主站的键驱逐时向副本发送DEL命令。

这种行为确保主站和副本保持一致，通常是你想要的，但是如果你的副本是可写的，或者你希望副本有不同的内存设置，并且你确信所有对副本的写入都是空闲的，那么你可以改变这个默认值（但是要确保理解你在做什么）。

请注意，由于副本默认不驱逐，它最终使用的内存可能比通过maxmemory设置的要多（有一些缓冲区在副本上可能更大，或者数据结构有时可能占用更多的内存等等）。因此，请确保你监控你的副本，并确保它们有足够的内存，在主站达到配置的最大内存设置之前，绝不会遇到真正的内存不足的情况。

```
# replica-ignore-maxmemory yes
```

Redis以两种方式回收过期的钥匙：当发现这些钥匙过期时，在访问时回收；也可以在后台回收，即所谓的 "主动过期钥匙"。密钥空间被缓慢地、交互式地扫描，寻找要回收的过期密钥，这样就有可能释放那些过期的、在短时间内不会再被访问的密钥的内存。

过期周期的默认努力将尽量避免有超过百分之十的过期钥匙仍在内存中，并将尽量避免消耗超过总内存的25%，并增加系统的延迟。然而，可以将通常设置为 "1 "的过期 "努力 "增加到更大的数值，最高为 "10 "值。在其最大值下，系统将使用更多的CPU，更长的周期（在技术上可能会引入更多的延迟），并将容忍更少的已经过期的钥匙仍然存在于系统中。这是内存、CPU和延迟之间的一个权衡。

```
# active-expire-effort 1
```

## LAZY FREEING

Redis有两个基元来删除键。一个叫做DEL，是对对象的阻塞式删除。这意味着服务器会停止处理新的命令，以便以同步的方式回收与对象相关的所有内存。如果删除的键与一个小对象相关联，那么执行DEL命令所需的时间非常小，与Redis中大多数其他O(1)或O(log_N)命令相当。然而，如果键与一个包含数百万元素的聚合值相关联，服务器可能会为了完成操作而阻塞很长时间（甚至几秒钟）。

由于上述原因，Redis还提供了非阻塞性的删除原语，如UNLINK（非阻塞性DEL）和FLUSHALL和FLUSHDB命令的ASYNC选项，以便在后台回收内存。这些命令是在恒定时间内执行的。另一个线程将以最快的速度在后台释放对象。

FLUSHALL和FLUSHDB的DEL、UNLINK和ASYNC选项是用户控制的。 这取决于应用程序的设计，以了解何时使用一个或另一个是个好主意。然而Redis服务器有时不得不删除键或刷新整个数据库，作为其他操作的副作用。 特别是Redis在以下情况下独立于用户调用而删除对象。

1. 在驱逐时，因为maxmemory和maxmemory策略的配置，为了给新的数据腾出空间，不超过指定的内存限制。
1. 因为expire：当一个有相关生存时间的键（见EXPIRE命令）必须从内存中删除。
1.  因为一个命令的副作用，在一个可能已经存在的键上存储数据。例如，RENAME命令在用另一个键替换时可能会删除旧的键内容。同样，SUNIONSTORE或带有STORE选项的SORT也可能删除现有的键。SET命令本身会删除指定键的任何旧内容，以便用指定的字符串替换它。
1.  在复制过程中，当一个副本与它的主副本进行完全的重新同步时，整个数据库的内容被删除，以便加载刚刚转移的RDB文件。

在上述所有情况下，默认是以阻塞的方式删除对象，就像调用DEL一样。然而，你可以使用下面的配置指令，具体配置每种情况，以便以非阻塞的方式释放内存，就像调用UNLINK一样。

```
lazyfree-lazy-eviction no
lazyfree-lazy-expire no
lazyfree-lazy-server-del no
replica-lazy-flush no
```

在用UNLINK调用取代用户代码DEL调用不容易的情况下，也可以用以下配置指令修改DEL命令的默认行为，使其与UNLINK完全一样。

```
lazyfree-lazy-user-del no
```

FLUSHDB、FLUSHALL和SCRIPT FLUSH同时支持异步和同步删除，这可以通过在命令中传递[SYNC|ASYNC]标志来控制。当这两个标志都没有通过时，该指令将被用来决定是否应该异步删除数据。

```
lazyfree-lazy-user-flush no
```

## THREADED I/O

Redis大部分是单线程的，然而有一些线程操作，如UNLINK、慢速I/O访问和其他一些在侧线程上执行的东西。

现在也可以在不同的I/O线程中处理Redis客户端套接字的读取和写入。由于写入速度很慢，通常Redis用户使用流水线，以加快Redis每个核心的性能，并催生多个实例，以扩展更多。使用I/O线程可以很容易地将Redis的速度提高2倍，而不需要借助管道或实例的分片。

默认情况下，线程是禁用的，我们建议只在至少有4个或更多核心的机器上启用它，并留下至少一个备用核心。使用8个以上的线程不太可能有什么帮助。我们还建议只有在你确实有性能问题时才使用线程I/O，Redis实例能够使用相当大比例的CPU时间，否则使用这个功能就没有意义。

因此，例如，如果你有一个四核的盒子，尽量使用2或3个I/O线程，如果你有一个8核的盒子，尽量使用6个线程。为了启用I/O线程，请使用以下配置指令。

```
# io-threads 4
```

将io-threads设置为1会像往常一样使用主线程。 当I/O线程被启用时，我们只使用线程进行写入，也就是对write(2)系统调用进行线程化，并将客户端的缓冲区传输到套接字。然而，也可以使用下面的配置指令来启用读取和协议解析的线程，将其设置为yes。

```
# io-threads-do-reads no
```

通常情况下，线程读取并没有什么帮助。

注意1：这个配置指令不能在运行时通过CONFIG SET改变。同样，这个功能目前在启用SSL时也不工作。

注意2：如果你想用redis-benchmark测试Redis的速度，请确保你也在线程模式下运行基准测试本身，使用--threads选项来匹配Redis的线程数，否则你将无法注意到改进


## KERNEL OOM CONTROL

在Linux上，有可能提示内核OOM杀手在内存耗尽时应该首先杀死哪些进程。

启用该功能使Redis主动控制其所有进程的oom_score_adj值，这取决于它们的角色。默认的分数将试图让后台子进程在所有其他进程之前被杀死，而复制体在主进程之前被杀死。

Redis支持三个选项。

- no:        不要对oom-score-adj进行修改（默认）。
- yes:      与 "相对 "的别名见下文。
- absolute: oom-score-adj-values中的数值是按原样写给内核的。
- relative: 值是相对于服务器启动时oom_score_adj的初始值使用的，然后被钳制在-1000到1000的范围内。因为通常初始值是0，所以它们通常会与绝对值相匹配。

```
oom-score-adj no
```

当使用oom-score-adj时，该指令控制用于主进程、副本和后台子进程的具体数值。值的范围是-2000到2000（越高意味着越有可能被杀死）。

没有特权的进程（非root，并且没有CAP_SYS_RESOURCE功能）可以自由地增加它们的值，但不能将其减少到低于其初始设置。这意味着将oom-score-adj设置为 "相对"，并将oom-score-adj-values设置为正值，总会成功。

```
oom-score-adj-values 0 200 800
```

## KERNEL transparent hugepage CONTROL

通常情况下，内核透明巨大页控制默认设置为 "madvise "或或 "never"（/sys/kernel/mm/transparent_hugepage/enabled），在这种情况下，这个配置没有影响。在它被设置为 "always "的系统中，redis 将试图专门为 redis 进程禁用它，以避免特别是 fork(2) 和 CoW 的延迟问题。 如果出于某种原因，你喜欢保持它的启用状态，你可以把这个配置设置为 "no"，把内核全局设置为 "always"。

```
disable-thp yes
```

## LUA SCRIPTING

一个Lua脚本的最大执行时间，以毫秒为单位。
如果达到了最大执行时间，Redis将记录一个脚本在允许的最大时间后仍在执行，并开始以错误回复查询。
当一个长期运行的脚本超过最大执行时间时，只有SCRIPT KILL和SHUTDOWN NOSAVE命令可用。第一个命令可以用来停止一个还没有调用任何写命令的脚本。第二条是在脚本已经发出写命令但用户不想等待脚本自然终止的情况下关闭服务器的唯一方法。
把它设置为0或负值，可以在没有警告的情况下无限制地执行。

```
lua-time-limit 5000
```


## SLOW LOG

Redis慢速日志是一个记录超过指定执行时间的查询的系统。执行时间不包括I/O操作，如与客户交谈、发送回复等，而只是实际执行命令所需的时间（这是命令执行的唯一阶段，线程被阻塞，在此期间不能为其他请求服务）。

你可以用两个参数来配置慢速日志：一个是告诉Redis要超过多少执行时间（以微秒为单位）才能将命令记录下来，另一个参数是慢速日志的长度。当一个新的命令被记录下来时，最旧的命令会从记录的命令队列中被删除。

下面的时间用微秒表示，所以1000000相当于一秒钟。请注意，一个负数将禁用慢速日志，而一个零值将强制记录每一条命令。

```
slowlog-log-slower-than 10000
```

这个长度是没有限制的。只是要注意它将消耗内存。你可以用SLOWLOG RESET来回收慢速日志所使用的内存。


```
slowlog-max-len 128
```

## LATENCY MONITOR

Redis延迟监控子系统在运行时对不同的操作进行采样，以收集与Redis实例的可能延迟来源有关的数据。

通过LATENCY命令，这些信息可供用户使用，用户可以打印图表和获得报告。

系统只记录那些在等于或大于通过latency-monitor-threshold配置指令指定的毫秒量的时间内执行的操作。当它的值被设置为零时，延迟监控被关闭。

默认情况下，延迟监控是禁用的，因为如果你没有延迟问题，它大多是不需要的，而且收集数据对性能有影响，虽然非常小，但可以在大负载下测量。如果需要的话，可以使用 "`CONFIG SET latency-monitor-threshold <milliseconds>`"命令在运行时轻松启用延迟监控。

```
latency-monitor-threshold 0
```



## REDIS CLUSTER


普通的 Redis 实例不能成为 Redis 集群的一部分；只有作为集群节点启动的节点可以。为了将Redis实例作为集群节点启动，启用集群支持，请取消以下注释。
```
# cluster-enabled yes
```
每个集群节点都有一个集群配置文件。这个文件不打算用手编辑。它是由Redis节点创建和更新的。每个Redis集群节点都需要一个不同的集群配置文件。确保在同一系统中运行的实例没有重叠的集群配置文件名称。

```
# cluster-config-file nodes-6379.conf
```
集群节点超时是指一个节点必须在无法到达的情况下被认为处于失败状态的毫秒数量。 大多数其他内部时间限制是节点超时的倍数。

```
# cluster-node-timeout 15000
```
如果一个失败的主站的副本的数据看起来太旧，它将避免启动故障转移。
对于一个副本来说，没有简单的方法可以真正准确地衡量其 "数据年龄"，所以要进行以下两个检查。

1. 如果有多个副本能够进行故障转移，它们会交换信息，以尝试给具有最佳复制偏移量（从主站处理的更多数据）的副本带来优势。复制体将尝试通过偏移量获得它们的排名，并在故障转移开始时应用与它们的排名成比例的延迟。
2. 每个副本都会计算与主站最后一次互动的时间。这可以是最后一次收到的ping或命令（如果主站仍然处于 "连接 "状态），或者是与主站断开连接后的时间（如果复制链路目前是关闭的）。如果最后的交互时间太长，复制体将根本不会尝试故障转移。

2 点可以由用户进行调整。具体来说，如果从最后一次与主站的互动开始，时间超过了，那么副本就不会执行故障转移。


```
(node-timeout * cluster-replica-validity-factor) + repl-ping-replica-period
```

因此，举例来说，如果节点超时是30秒，集群-副本-有效性因素是10，并假设默认的repl-ping-replica-period为10秒，如果副本无法与主站对话的时间超过310秒，它将不会尝试故障转移。

一个大的cluster-replica-validity-factor可能允许数据太旧的复制体故障转移主站，而一个太小的值可能使集群根本无法选出一个复制体。

为了获得最大的可用性，可以将cluster-replica-validity-factor设置为0，这意味着，无论复制体最后一次与主站交互是什么时候，它们都会尝试故障转移主站。 (然而，它们总是试图应用一个与它们的偏移等级成比例的延迟）。)

零是唯一能够保证当所有分区愈合时，集群总是能够继续下去的数值。

```
# cluster-replica-validity-factor 10
```

集群副本能够迁移到孤儿主站，即没有工作副本的主站。这提高了集群抵御故障的能力，否则，如果没有工作的复制，孤儿主站就不能在故障情况下被转移。

复制品只有在其旧主站至少还有一定数量的其他工作复制品的情况下才会迁移到无主站。这个数字就是 "迁移障碍"。迁移障碍为1，意味着只有当主站至少有1个其他工作副本时，副本才会迁移，以此类推。它通常反映了你希望集群中每个主站的复制数量。

默认值是1（只有当主站仍有至少一个副本时，副本才会迁移）。要禁用迁移，只需将其设置为一个非常大的值，或者将cluster-allow-replica-migration设置为'no'。 可以设置为0，但只对调试有用，在生产中是很危险的。

```
# cluster-migration-barrier 1
```

关掉这个选项可以减少自动集群配置的使用。 它既禁止向无主的主站迁移，也禁止从变成空的主站迁移。 默认是 "是"（允许自动迁移）。

```
# cluster-allow-replica-migration yes
```

默认情况下，如果Redis集群节点检测到至少有一个哈希槽未被覆盖（没有可用的节点为其服务），则停止接受查询。 这样，如果集群部分瘫痪（例如，一系列的哈希槽不再被覆盖），所有的集群最终会变得不可用。 一旦所有的槽被覆盖，它就会自动恢复可用。

然而，有时你想让正在工作的集群的子集继续接受对仍被覆盖的密钥空间部分的查询。为了做到这一点，只需将cluster-require-full-coverage选项设置为no。

```
# cluster-require-full-coverage yes
```

这个选项，当设置为 "是 "时，防止复制体在主站故障时尝试故障转移其主站。然而，如果被迫这样做，复制体仍然可以执行手动故障转移。

这在不同的场景下是很有用的，特别是在多个数据中心运行的情况下，如果不是在DC完全故障的情况下，我们希望一方永远不会被提升。


```
# cluster-replica-no-failover no
```

这个选项设置为 "是 "时，允许节点在集群处于停机状态时提供读取流量，只要它认为自己拥有这些槽位。

这在两种情况下是有用的。 第一种情况是，当一个应用程序在节点故障或网络分区时不需要数据的一致性。 这方面的一个例子是缓存，只要节点拥有数据，它就应该能够为其服务。

第二个用例是不符合推荐的三个分片的配置，但想启用集群模式，以后再进行扩展。在1或2个分片的配置中，如果没有设置这个选项，主控器的中断会导致整个集群的读/写中断，如果设置了这个选项，就只有写中断了。 如果没有主站的法定人数，插槽所有权将不会自动改变。

```
# cluster-allow-reads-when-down no
```

为了设置你的集群，请确保阅读 https://redis.io 网站上的文档。

## CLUSTER DOCKER/NAT support


在某些部署中，Redis Cluster节点的地址发现失败，因为地址被NAT-ted或端口被转发（典型的情况是Docker和其他容器）。

为了使Redis Cluster在这样的环境中工作，需要一个静态配置，每个节点都知道它的公共地址。以下四个选项用于这个范围，它们是：。


* cluster-announce-ip
* cluster-announce-port
* cluster-announce-tls-port
* cluster-announce-bus-port


每个节点都指示它的地址、客户端端口（用于没有TLS和有TLS的连接）和集群消息总线端口。然后，这些信息被公布在总线数据包的头部，以便其他节点能够正确映射公布信息的节点的地址。

如果cluster-tls被设置为yes，而cluster-announce-tls-port被省略或设置为0，那么cluster-announce-port指的是TLS端口。还要注意的是，如果cluster-tls被设置为no，cluster-announce-tls-port就没有作用。
如果不使用上述选项，将使用正常的Redis集群自动检测。

注意，当重新映射时，总线端口可能不在client port + 10000的固定偏移处，所以你可以指定任何端口和总线端口，这取决于它们如何被重新映射。如果没有设置总线端口，将照常使用10000的固定偏移。


Example:

```
# cluster-announce-ip 10.1.1.5
# cluster-announce-tls-port 6379
# cluster-announce-port 0
# cluster-announce-bus-port 6380
```

## 事件通知


Redis可以通知Pub/Sub客户端在密钥空间中发生的事件。 这个功能的文档在https://redis.io/topics/notifications

例如，如果钥匙空间事件通知被启用，一个客户端对存储在数据库0中的钥匙 "foo "执行DEL操作，两个消息将通过Pub/Sub发布。

```
# PUBLISH __keyspace@0__:foo del
# PUBLISH __keyevent@0__:del foo
```

可以在一组类中选择Redis将通知的事件。每个类都由一个字符来标识。

- K     Keyspace events, published with __keyspace@<db>__ prefix.
- E     Keyevent events, published with __keyevent@<db>__ prefix.
- g     Generic commands (non-type specific) like DEL, EXPIRE, RENAME, ...
- $     String commands
- l     List commands
- s     Set commands
- h     Hash commands
- z     Sorted set commands
- x     Expired events (events generated every time a key expires)
- e     Evicted events (events generated when a key is evicted for maxmemory)
- t     Stream commands
- d     Module key type events
- m     Key-miss events (Note: It is not included in the 'A' class)
- A     Alias for g$lshzxetd, so that the "AKE" string means all the events (Except key-miss events which are excluded from 'A' due to their  unique nature).

notify-keyspace-events 的参数是一个由零或多个字符组成的字符串。空字符串意味着通知被禁用。

例如：要启用列表和通用事件，从事件名称的角度来看，使用。

```
#  notify-keyspace-events Elg
```

例2：为了获得订阅通道名称为__keyevent@0__:expired的过期钥匙流，使用。

```
#  notify-keyspace-events Ex
```

默认情况下，所有的通知都是禁用的，因为大多数用户不需要这个功能，而且这个功能有一些开销。注意，如果你没有指定K或E中的至少一个，就不会有事件被传递。

```
notify-keyspace-events ""
```

## GOPHER SERVER


Redis包含Gopher协议的实现，如RFC 1436（https://www.ietf.org/rfc/rfc1436.txt）所规定。

Gopher协议在90年代末非常流行。它是网络的替代品，服务器和客户端的实现都非常简单，Redis服务器只有100行代码来实现这种支持。

现在你用Gopher做什么？好吧，Gopher从来没有*真正的死亡，最近有一个运动，以使Gopher更多的层次内容组成的纯文本文件复活。一些人希望有一个更简单的互联网，另一些人则认为主流互联网变得太受控制了，而为那些想要一点新鲜空气的人创造一个替代空间是很酷的。

总之，为了Redis的10岁生日，我们把Gopher协议作为礼物送给它。

它是如何运作的？

Redis Gopher支持使用Redis的内联协议，特别是两种内联请求，无论如何都是非法的：空请求或任何以"/"开头的请求（没有以这种斜线开头的Redis命令）。正常的RESP2/RESP3请求完全不在Gopher协议实现的范围内，也会照常提供。

如果你在Gopher启用时打开与Redis的连接，并向它发送一个类似"/foo "的字符串，如果有一个名为"/foo "的键，它将通过Gopher协议提供服务。

为了创建一个真正的Gopher "洞"（Gopher谈话中的Gopher网站的名称），你可能需要一个像下面的脚本。

https://github.com/antirez/gopher2redis

安全警告

如果你打算把 Redis 放在互联网上的一个可公开访问的地址上，以便为 Gopher 网页提供服务，一定要为实例设置密码。 一旦设置了密码。

1. Gopher服务器（当启用时，不是默认的）仍将通过Gopher提供内容。
2. 2.然而，在客户端进行认证之前，不能调用其他命令。

所以使用'requirepass'选项来保护你的实例。

注意，当 "io-threads-do-reads "被启用时，目前不支持Gopher。

要启用对Gopher的支持，请取消对下面一行的注释，并将选项从no（默认）设置为yes。

```
# gopher-enabled no
```

## 高级配置



当哈希值有少量的条目，并且最大的条目不超过一个给定的阈值时，哈希值就会使用一个内存效率高的数据结构进行编码。这些阈值可以通过以下指令来配置。

```
hash-max-ziplist-entries 512
hash-max-ziplist-value 64
```

列表也以一种特殊的方式进行编码，以节省大量的空间。 每个内部列表节点允许的条目数可以被指定为一个固定的最大尺寸或最大元素数。


对于一个固定的最大尺寸，使用-5到-1，意思是。

- `-5` : max size: 64 Kb  <-- not recommended for normal workloads
- `-4` : max size: 32 Kb  <-- not recommended
- `-3` : max size: 16 Kb  <-- probably not recommended
- `-2` : max size: 8 Kb   <-- good
- `-1` : max size: 4 Kb   <-- good

正数意味着每一个列表节点最多存储_个元素的数量。 性能最高的选项通常是-2（8 Kb大小）或-1（4 Kb大小），但如果你的用例是独特的，可根据需要调整设置。


```
list-max-ziplist-size -2
```

Lists may also be compressed.

Compress depth is the number of quicklist ziplist nodes from **each** side of  the list to **exclude** from compression.  The head and tail of the list  are always uncompressed for fast push/pop operations.  Settings are:

-  0: disable all list compression
-  1: depth 1 means "don't start compressing until after 1 node into the list, going from either the head or tail" So: [head]->node->node->...->node->[tail] [head], [tail] will always be uncompressed; inner nodes will compress.
-  2: [head]->[next]->node->node->...->node->[prev]->[tail] 2 here means: don't compress head or head->next or tail->prev or tail, but compress all nodes between them.
-  3: [head]->[next]->[next]->node->node->...->node->[prev]->[prev]->[tail]

```
list-compress-depth 0
```

集合只在一种情况下有特殊的编码：当一个集合只是由刚好是64位有符号整数范围内的radix 10的字符串组成时。 下面的配置设置设置了集合的大小限制，以便使用这种特殊的节省内存的编码。

```
set-max-intset-entries 512
```

与哈希值和列表类似，排序集也被特别编码，以节省大量空间。这种编码只有在排序集的长度和元素低于以下限制时才会使用。

```
zset-max-ziplist-entries 128
zset-max-ziplist-value 64
```

HyperLogLog稀疏表示法的字节数限制。这个限制包括16字节的标题。当使用稀疏表示法的HyperLogLog超过这个限制时，它就会被转换为密集表示法。

一个大于16000的值是完全无用的，因为在这一点上，稠密表示法的内存效率更高。

建议的值是~3000，以获得空间效率高的编码的好处，同时又不会使PFADD的速度太慢，因为稀疏编码的速度是O(N)。当CPU不是一个问题，但空间是一个问题，并且数据集是由许多cardinality在0-15000范围内的HyperLogLogs组成时，该值可以提高到~10000。



```
hll-sparse-max-bytes 3000
```

流的大节点最大尺寸/项目。流数据结构是一个大节点的弧度树，里面编码了多个项目。使用这个配置可以配置单个节点的字节数，以及在追加新的流条目时切换到新的节点之前它可能包含的最大项目数。如果下面的任何设置被设置为0，那么该限制将被忽略，因此，例如，可以通过将max-byte设置为0，将max-entries设置为所需的值来设置最大条目限制。


```
stream-node-max-bytes 4096
stream-node-max-entries 100
```


主动重洗每100毫秒使用1毫秒的CPU时间，以帮助重洗Redis的主哈希表（将顶层键映射到值的哈希表）。Redis使用的哈希表实现（见dict.c）执行了一个懒惰的重洗：你越是碰到正在重洗的哈希表的操作，就会执行更多的重洗 "步骤"，所以如果服务器是空闲的，重洗就永远不会完成，还有一些内存被哈希表使用。


默认情况下是每秒使用这个毫秒10次，以便主动重新洗练主字典，尽可能释放内存。

如果不确定：如果你有硬性的延迟要求，并且Redis不时回复2毫秒延迟的查询在你的环境中不是一件好事，则使用 "activerehashing no"。

如果你没有这样的硬性要求，但想尽快释放内存，则使用 "activeerehashing yes"。

```
activerehashing yes
```


客户端输出缓冲区的限制可以用来强制断开那些由于某种原因不能从服务器上快速读取数据的客户端（一个常见的原因是Pub/Sub客户端消耗消息的速度比不上发布者产生消息的速度）。

对于三类不同的客户，可以设置不同的限额。

-  normal -> normal clients including MONITOR clients
-  replica  -> replica clients
-  pubsub -> clients subscribed to at least one pubsub channel or pattern

每条client-output-buffer-limit指令的语法如下。

```
# client-output-buffer-limit <class> <hard limit> <soft limit> <soft seconds>
```

一旦达到硬限制，或达到软限制并保持在指定的秒数（连续），客户端将立即断开连接。 因此，例如，如果硬限制是32兆字节，软限制是16兆字节/10秒，如果输出缓冲区的大小达到32兆字节，客户端将立即断开连接，但如果客户端达到16兆字节并连续克服10秒的限制，也将被断开连接。

默认情况下，正常的客户端不会受到限制，因为他们不会在没有询问的情况下接收数据（以推送的方式），而只是在请求之后，所以只有异步客户端可能会创造一种情况，即请求数据的速度比它能够读取的速度快。

相反，对于pubsub和replica客户端有一个默认的限制，因为订阅者和复制者是以推送方式接收数据。
硬限制或软限制都可以通过设置为零来禁用。

```
client-output-buffer-limit normal 0 0 0
client-output-buffer-limit replica 256mb 64mb 60
client-output-buffer-limit pubsub 32mb 8mb 60
```

客户端查询缓冲区会积累新的命令。默认情况下，它们被限制在一个固定的数量，以避免协议的不同步（例如由于客户端的一个错误）会导致查询缓冲区的内存使用不受限制。然而，如果你有非常特殊的需求，如巨大的多/执行请求或类似的需求，你可以在这里配置它。


```
# client-query-buffer-limit 1gb
```

在Redis协议中，批量请求，也就是代表单个字符串的元素，通常被限制在512 mb。然而，你可以在这里改变这个限制，但必须是1mb或更大。

```
# proto-max-bulk-len 512mb
```

Redis调用一个内部函数来执行许多后台任务，如关闭超时的客户端连接，清除从未被请求的过期密钥，等等。

并非所有的任务都以相同的频率执行，但Redis会根据指定的 "hz "值检查要执行的任务。

默认情况下，"hz "被设置为10。提高该值会在Redis空闲时使用更多的CPU，但同时会使Redis在有许多键同时到期时反应更快，而且超时的处理可能更精确。

范围在1到500之间，然而超过100的值通常不是一个好主意。大多数用户应该使用默认的10，只有在需要非常低的延迟的环境下，才可以将其提高到100。

```
hz 10
```

通常情况下，HZ值与连接的客户数量成正比是很有用的。这是有用的，例如，为了避免每个后台任务的调用有太多的客户被处理，以避免延迟高峰的出现。

由于默认的HZ值被保守地设置为10，Redis提供并默认启用了使用自适应HZ值的能力，当有许多连接的客户端时，它会暂时提高HZ值。

当启用动态HZ时，实际配置的HZ将被用作基线，但一旦有更多的客户端连接，配置的HZ值的倍数将根据需要被实际使用。通过这种方式，一个空闲的实例将使用很少的CPU时间，而一个繁忙的实例将有更多的响应。

```
dynamic-hz yes
```

当一个孩子重写AOF文件时，如果启用以下选项，该文件将每产生32MB的数据就进行一次sync。这对于将文件更多地提交到磁盘并避免大的延迟峰值是很有用的。

```
aof-rewrite-incremental-fsync yes
```

当redis保存RDB文件时，如果启用以下选项，该文件将每产生32MB的数据就进行一次sync。这对于将文件更多地提交到磁盘并避免大的延迟峰值是很有用的。


```
rdb-save-incremental-fsync yes
```

Redis的LFU驱逐（见maxmemory设置）是可以调整的。然而，从默认设置开始是一个好主意，只有在调查了如何改善性能和钥匙LFU随时间的变化后才可以改变它们，这可以通过OBJECT FREQ命令来检查。

在Redis LFU的实现中，有两个可调整的参数：计数器的对数因子和计数器的衰减时间。在改变这两个参数之前，理解这两个参数的含义是很重要的。

LFU计数器每个键只有8位，它的最大值是255，所以Redis使用了一个具有对数行为的概率递增。考虑到旧的计数器的值，当一个键被访问时，计数器会以这种方式递增。


-  1. A random number R between 0 and 1 is extracted.
-  2. A probability P is calculated as 1/(old_value*lfu_log_factor+1).
-  3. The counter is incremented only if R < P.

默认的lfu-log系数是10。这是一个表格，说明频率计在不同的访问次数下，不同的对数因子如何变化。

```
# +--------+------------+------------+------------+------------+------------+
# | factor | 100 hits   | 1000 hits  | 100K hits  | 1M hits    | 10M hits   |
# +--------+------------+------------+------------+------------+------------+
# | 0      | 104        | 255        | 255        | 255        | 255        |
# +--------+------------+------------+------------+------------+------------+
# | 1      | 18         | 49         | 255        | 255        | 255        |
# +--------+------------+------------+------------+------------+------------+
# | 10     | 10         | 18         | 142        | 255        | 255        |
# +--------+------------+------------+------------+------------+------------+
# | 100    | 8          | 11         | 49         | 143        | 255        |
# +--------+------------+------------+------------+------------+------------+
```

注意：上表是通过运行以下命令得到的。

```
#   redis-benchmark -n 1000000 incr foo
#   redis-cli object freq foo
```

注意2：计数器的初始值是5，以便给新的物体一个机会来积累点击率。

计数器的衰减时间是指必须经过的时间，以分钟为单位，以便钥匙计数器被除以2（如果它的值小于<=10，则被递减）。

lfu-decay-time的默认值是1。一个特殊的值0意味着每次碰巧被扫描时都要对计数器进行衰减。


```
# lfu-log-factor 10
# lfu-decay-time 1
```

## 主动碎片整理


什么是主动碎片整理


主动（在线）碎片整理允许Redis服务器压缩内存中小的数据分配和取消分配之间的空间，从而允许回收内存。

分片是一个自然过程，每个分配器（但幸运的是，Jemalloc较少）和某些工作负载都会发生。通常情况下，为了降低碎片化，需要重新启动服务器，或者至少要冲走所有的数据并重新创建它。然而，由于Oran Agra为Redis 4.0实现的这个功能，这个过程可以在运行时以 "热 "的方式发生，而服务器正在运行。

基本上，当碎片化超过一定程度时（见下面的配置选项），Redis将开始利用某些特定的Jemalloc功能在连续的内存区域创建新的值副本（以了解分配是否导致碎片化并将其分配在更好的地方），同时，将释放数据的旧副本。这个过程，对所有的键重复递增，将导致碎片化下降到正常值。

需要了解的重要事项

1. 这个功能默认是禁用的，只有在你编译Redis时使用我们随Redis源代码提供的Jemalloc副本时才会工作。这是在Linux构建中的默认设置。

2. 如果你没有碎片问题，你永远不需要启用这个功能。

3. 一旦你遇到碎片问题，你可以在需要时用 `CONFIG SET activedefrag yes `命令启用这个功能。

配置参数能够对碎片整理过程的行为进行微调。如果你不确定它们的含义，最好不要动用默认值。

启用了主动碎片整理功能

```
# activedefrag no
```
启动主动碎片整理的最小碎片量
```
# active-defrag-ignore-bytes 100mb
```
启动主动碎片整理的最小碎片百分比

```
# active-defrag-threshold-lower 10
```

在我们使用最大努力的情况下，碎片化的最大百分比
```
# active-defrag-threshold-upper 100
```
以CPU百分比计算的碎片整理的最小努力，在达到较低的阈值时使用。
```
# active-defrag-cycle-min 1
```
以CPU百分比计算的碎片整理的最大努力，在达到上限时使用。
```
# active-defrag-cycle-max 25
```

将从主词典扫描中处理的set/hash/zset/list字段的最大数量
```
# active-defrag-max-scan-fields 1000
```
用于清除的Jemalloc后台线程将被默认启用
```
jemalloc-bg-thread yes
```

可以将Redis的不同线程和进程钉在系统的特定CPU上，以最大限度地提高服务器的性能。 这对于将不同的Redis线程固定在不同的CPU上是很有用的，同时也可以确保在同一主机上运行的多个Redis实例被固定在不同的CPU上。


通常情况下，你可以使用 "taskset "命令来做到这一点，但也可以通过Redis配置直接做到这一点，在Linux和FreeBSD中都是如此。

你可以锁定服务器/IO线程、bio线程、aof重写子进程和bgsave子进程。指定cpu列表的语法与taskset命令相同。

将redis-server/io线程设置为cpu亲和力0、2、4、6。
```
# server_cpulist 0-7:2
```

将生物线程设置为cpu亲和力1,3。

```
# bio_cpulist 1,3
```
设置aof重写子进程为cpu亲和力8,9,10,11。
```
# aof_rewrite_cpulist 8-11
```
设置bgsave子进程为cpu亲和力1,10,11
```
# bgsave_cpulist 1,10-11
```

在某些情况下，如果redis检测到系统处于不良状态，它将发出警告，甚至拒绝启动，可以通过设置以下配置来抑制这些警告，该配置需要一个以空格分隔的警告列表来抑制这些警告

```
# ignore-warnings ARM64-COW-BUG
```

## TLS/SSL

默认情况下，TLS/SSL被禁用。要启用它，可以使用 "tls-port "配置指令来定义TLS监听端口。要在默认端口上启用TLS，请使用。

```
# port 0
# tls-port 6379
```

配置一个X.509证书和私钥，用于向连接的客户、主站或集群对等体验证服务器。 这些文件应该是PEM格式的。

```
# tls-cert-file redis.crt
# tls-key-file redis.key
```

如果密钥文件是用口令加密的，也可以包括在这里。

```
# tls-key-file-pass secret
```

通常Redis在服务器功能（接受连接）和客户端功能（从主站复制，建立集群总线连接等）上使用相同的证书。

有时颁发的证书具有指定它们为仅适用于客户或仅适用于服务器证书的属性。在这种情况下，可能需要为传入（服务器）和传出（客户端）的连接使用不同的证书。要做到这一点，请使用以下指令。

```
# tls-client-cert-file client.crt
# tls-client-key-file client.key
```

如果密钥文件是用口令加密的，也可以包括在这里。

```
# tls-client-key-file-pass secret
```

配置一个DH参数文件以启用Diffie-Hellman（DH）密钥交换。

```
# tls-dh-params-file redis.dh
```

配置一个CA证书包或目录，以验证TLS/SSL客户和对等体。 Redis需要明确配置其中的至少一个，而不会隐含地使用系统范围内的配置。

```
# tls-ca-cert-file ca.crt
# tls-ca-cert-dir /etc/ssl/certs
```

默认情况下，TLS端口的客户端（包括复制服务器）需要使用有效的客户端证书进行认证。

如果指定了 "no"，则不需要也不接受客户端证书。如果指定 "可选"，则接受客户端证书，如果提供的话必须是有效的，但不是必须的。

```
# tls-auth-clients no
# tls-auth-clients optional
```

默认情况下，Redis副本不会尝试与它的主站建立TLS连接。

使用下面的指令，在复制链接上启用TLS。

```
# tls-replication yes
```

默认情况下，Redis Cluster总线使用普通的TCP连接。要为总线协议启用TLS，请使用以下指令。


```
# tls-cluster yes
```

默认情况下，只有TLSv1.2和TLSv1.3被启用，强烈建议保留旧的正式废弃的版本以减少攻击面。你可以明确指定要支持的TLS版本。允许的值不区分大小写，包括 "TLSv1"、"TLSv1.1"、"TLSv1.2"、"TLSv1.3"（OpenSSL >= 1.1.1）或任何组合。要只启用TLSv1.2和TLSv1.3，请使用。


```
# tls-protocols "TLSv1.2 TLSv1.3"
```

配置允许的密码。 关于这个字符串的语法，请参见ciphers(1ssl)手册的更多信息。

注意：该配置仅适用于<= TLSv1.2。

```
# tls-ciphers DEFAULT:!MEDIUM
```

配置允许的 TLSv1.3 密码套件。 关于这个字符串的语法的更多信息，请参见ciphers(1ssl)手册，特别是关于TLSv1.3密码套件。


```
# tls-ciphersuites TLS_CHACHA20_POLY1305_SHA256
```

当选择一个密码时，使用服务器的偏好，而不是客户的偏好。默认情况下，服务器会遵循客户端的偏好。

```
# tls-prefer-server-ciphers yes
```

默认情况下，TLS会话缓存被启用，以允许支持它的客户更快和更便宜地进行连接。使用下面的指令来禁用缓存。

```
# tls-session-caching no
```

改变默认的TLS会话缓存的数量。零值将缓存设置为无限大小。默认的大小是20480。

```
# tls-session-cache-size 5000
```

改变缓存TLS会话的默认超时时间。默认超时时间是300秒。


```
# tls-session-cache-timeout 60
```
