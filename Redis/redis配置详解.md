# redis配置详解

本文对redis配置的说明使用的是2.8.19版本。

## 配置单位说明

redis内存配置的单位可以支持直接填写字节，也可以填写以下单位：

- 1k => 1000 bytes
- 1kb => 1024 bytes
- 1m => 1000000 bytes
- 1mb => 1024 * 1024 bytes
- 1g => 1000000000 bytes
- 1gb => 1024 *1024* 1024 bytes

单位是大小写不敏感的，所以1GB 1Gb 1gB可以认为是一样的。

## INCLUDES配置

可以把其他的配置文件包含在该配置文件里面。这样如果有一个标准的配置模板，每个redis可以include这个模板，然后填写个性化的配置。

需要注意的是，include配置不会被”CONFIG REWRITE”命令重写进配置。redis重写是把更新的数据写入从最后一行开始写入，所以使用include的话，最好把include放在最前面

### include

- 说明：指定包含其它的配置文件，可以在同一主机上多个Redis实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件。
- 默认值:空。
- 是否可以动态修改:no。
- 值的范围:文件路径。

## 通用配置

下面是一些redis的通用配置。

### daemonize

- 说明：默认情况下，redis不是在后台运行的，如果需要在后台运行，把该项的值更改为yes。
- 默认值:no。
- 是否可以动态修改:no。
- 值的范围:yes|no。

### pidfile

- 说明：redis使用守护进程执行的时候保存的pid文件。
- 默认值:/var/run/redis.pid。
- 是否可以动态修改:no。
- 值的范围:文件路径。

### port

- 说明：redis监听的端口号。
- 默认值:6379。
- 是否可以动态修改:no。
- 值的范围:0到65535。

### tcp-backlog

- 说明：在高吞吐环境下，你需要更高的backlog来避免缓慢的客户端连接问题。tcp-backlog可能被Linux内核/proc/sys/net/core/somaxconn的值截断。backlog取的是tcp-backlog和/proc/sys/net/core/somaxconn的更小值。
- 默认值:511。
- 是否可以动态修改:no。
- 值的范围:大于等于0。

### bind

- 说明：redis默认是监听所有网络地址过来的连接。但是有可能有只需要监听一个网络地址或者多个地址过来的连接。bind参数可以配置多个监听地址。
- 默认值:空。
- 是否可以动态修改:no。
- 值的范围:IP地址。

### unixsocket

- 说明：配置unix域socket来让redis支持监听本地连接。这个参数没有默认值，所以redis不会使用unix域socket监听本地连接。
- 默认值:空。
- 是否可以动态修改:no。
- 值的范围:文件地址。

### unixsocketperm

- 说明：配置unix域socket使用文件的权限。
- 默认值:0。
- 是否可以动态修改:no。
- 值的范围:文件权限。

### timeout

- 说明：此参数为设置客户端空闲超过timeout，服务端会断开连接，为0则服务端不会主动断开连接，不能小于0。
- 默认值:0。
- 是否可以动态修改:yes。
- 值的范围:大于等于0。

### tcp-keepalive

- 说明：tcp keepalive参数。如果设置不为0，就使用配置tcp的SO_KEEPALIVE值，使用keepalive有两个好处:检测挂掉的对端。降低中间设备出问题而导致网络看似连接却已经与对端端口的问题。在Linux内核中，设置了keepalive，redis会定时给对端发送ack。检测到对端关闭需要两倍的设置值。
- 默认值:0。
- 是否可以动态修改:yes。
- 值的范围:大于等于0。

### loglevel

- 说明：指定了服务端日志的级别。级别包括：debug（很多信息，方便开发、测试），verbose（许多有用的信息，但是没有debug级别信息多），notice（适当的日志级别，适合生产环境），warn（只有非常重要的信息）
- 默认值:notice。
- 是否可以动态修改:yes。
- 值的范围:debug|verbose|notice|warn。

### logfile

- 说明：指定了记录日志的文件。空字符串的话，日志会打印到标准输出设备。后台运行的redis标准输出是/dev/null。
- 默认值:””。
- 是否可以动态修改:no。
- 值的范围:文件地址。

下面三个是syslog相关的配置。要把日志记录到syslog，只需要打开syslog-enabled开关，然后也有些可选的参数来适应你的需求。

### syslog-enabled

- 说明：是否打开记录syslog功能。
- 默认值:no。
- 是否可以动态修改:no。
- 值的范围:yes|no。

### syslog-ident

- 说明：syslog的标识符。
- 默认值:redis。
- 是否可以动态修改:no。
- 值的范围:任意字符串。

### syslog-facility

- 说明：日志的来源或设备。
- 默认值:local0。
- 是否可以动态修改:no。
- 值的范围:user|local0|local1|local2|local3|local4|local5|local6|local7

### databases

- 说明：设置数据库的数量，默认使用的数据库是DB 0。可以通过”SELECT “命令选择一个db，db的范围是0到databases-1。
- 默认值:16。
- 是否可以动态修改:no。
- 值的范围:大于0。

## rdb相关配置

rdb是用来把redis内存的数据保存到硬盘上的文件格式。

### save

- 说明：命令格式为：”save \ \“。该命令可以把内存的数据保存到硬盘。只要满足在指定的seconds内，写入命令个数大于changes，就会执行保存操作。配置文件默认是开了三种保存类型，对于不希望执行保存操作的用户最好注释掉这些配置。
- 默认值:
  - save 900 1
  - save 300 10
  - save 60 10000
- 是否可以动态修改:yes。
- 值的范围:大于0的数字加空格加大于等于0的数字。

### stop-writes-on-bgsave-error

- 说明：如果最后一次的后台保存RDB snapshot出错，redis就会拒绝所有写请求。这样也相当于一个报警吧。等后台保存继续工作后，redis就允许写了。如果你自己配置好了redis的持久化进程的监控，你最好关闭这个功能，这样即使磁盘有问题，redis还会继续工作。
- 默认值:yes。
- 是否可以动态修改:yes。
- 值的范围:yes|no。

### rdbcompression

- 说明：使用压缩rdb文件，rdb文件压缩使用LZF压缩算法。正常都会开启压缩。如果你想节省一些CPU，那可以考虑关闭该功能，如果你的key、value都是可压缩的，数据集也会变得很大。
- 默认值:yes。
- 是否可以动态修改:yes。
- 值的范围:yes|no。

### rdbchecksum

- 说明：是否校验rdb文件。从rdb格式的第五个版本开始，在rdb文件的末尾会带上CRC64的校验和。这跟有利于文件的容错性，但是在保存rdb文件的时候，会有大概10%的性能损耗，所以如果你追求高性能，可以关闭该配置。
- 默认值:yes。
- 是否可以动态修改:no。
- 值的范围:yes|no。

### dbfilename

- 说明：rdb文件的名称。
- 默认值:dump.rdb。
- 是否可以动态修改:yes。
- 值的范围:文件地址。

### dir

- 说明：工作路径，数据库的写入会在这个目录。aof文件也会写在这个目录下，注意：这里填的是目录地址，而不是文件地址。
- 默认值:./
- 是否可以动态修改:yes。
- 值的范围:目录地址。

## 复制相关配置

Master-Slave复制使用slaveof参数来让一个redis复制另一个redis。复制的话需要注意下面一下事情：

- 1、redis复制是异步的，但是你可以在master上配置，如果正常的slave数量不足时禁止写入。
- 2、slave如果和master连接断开，可以尝试部分同步（psync），如果断开时间很短，可能能马上同步成功。这也取决于backlog配置的size大小。
- 3、复制是自动完成的，不需要用户干预，slave与master网络异常，slave会自动重连。

### slaveof

- 说明：格式为”slaveof “，slave复制对应的master。
- 默认值:空。
- 是否可以动态修改:yes。
- 值的范围:masterip masterport。

### masterauth

- 说明：如果master设置了requirepass，那么slave要连上master，需要有master的密码才行。masterauth就是用来配置master的密码，这样可以在连上master后进行认证。
- 默认值:空。
- 是否可以动态修改:yes。
- 值的范围:master-password。

### slave-serve-stale-data

- 说明：当从库同主机失去连接或者复制正在进行，从机库有两种运行方式：1) 如果slave-serve-stale-data设置为yes(默认设置)，从库会继续响应客户端的请求。2) 如果slave-serve-stale-data设置为no，除去INFO和SLAVOF命令之外的任何请求都会返回一个错误”SYNC with master in progress”。
- 默认值:yes。
- 是否可以动态修改:yes。
- 值的范围:yes|no。

### slave-read-only

- 说明：slave是否只读。默认slave是只读的，可设置no，那么slave也能写入数据。一些临时数据可以保存在slave上（slave重连后这些数据会被清除），不过这也容易带来配置的混淆，因为客户端可能往slave上写数据了。在redis的设计上，只读的redis不应该暴露给不信任的客户端。只读只是为了防止对redis的滥用。只读的slave默认是支持所有的管理命令，比如CONFIG，DEBUG。如果要限制这些命令，可以使用’rename-command’配置隐藏管理命令。
- 默认值:yes。
- 是否可以动态修改:yes。
- 值的范围:yes|no。

目前redis复制提供两种方式，disk和socket。socket方式目前还处于实验阶段。如果新的slave连上来或者重连的slave无法部分同步，就会执行全量同步，master会生成rdb文件，disk方式是master创建一个新的进程把rdb文件保存到磁盘，再把磁盘上的rdb文件传递给slave。socket是master创建一个新的进程，直接把rdb文件以socket的方式发给slave。dis方式的时候，当一个rdb保存的过程中，多个slave都能共享这个rdb文件。socket的方式就的一个个slave顺序复制。在磁盘速度缓慢，网速快的情况下推荐用socket方式。

### repl-diskless-sync

- 说明：是否使用socket方式复制数据。
- 默认值:no。
- 是否可以动态修改:yes。
- 值的范围:yes|no。

### repl-diskless-sync-delay

- 说明：diskless复制的延迟时间，防止设置为0，复制开始的过快。一旦复制开始，节点不会再接收新slave的复制请求直到下一个rdb传输。所以最好等待一段时间，等更多的slave连上来。
- 默认值:5。
- 是否可以动态修改:yes。
- 值的范围:大于等于0。

### repl-ping-slave-period

- 说明：master给slave发送ping命令的频率，默认是10s发一个ping命令。
- 默认值:10。
- 是否可以动态修改:yes。
- 值的范围:大于0。

### repl-timeout

- 说明：复制链接超时时间。master和slave都有超时时间的设置。master检测到slave上次发送发送replconf命令的时间超过repl-timeout，即认为slave离线，清除该slave信息。slave检测到上次和master交互的时间超过repl-timeout，则认为master离线。需要注意的是repl-timeout需要设置一个比repl-ping-slave-period更大的值，不然会经常检测到超时。
- 默认值:60
- 是否可以动态修改:yes。
- 值的范围:大于0。

### repl-disable-tcp-nodelay

- 说明：是否禁止复制tcp链接的tcp nodelay参数，可传递yes或者no。默认是no，即使用tcp nodelay。如果master设置了yes来禁止tcp nodelay设置，在把数据复制给slave的时候，会减少包的数量和更小的网络带宽。但是这也可能带来数据的延迟。默认我们推荐更小的延迟，但是在数据量传输很大的场景下，建议选择yes。
- 默认值:no。
- 是否可以动态修改:yes。
- 值的范围:yes|no。

### repl-backlog-size

- 说明：复制缓冲区大小，这是一个环形复制缓冲区，用来保存最新复制的命令。这样在slave离线的时候，不需要完全复制master的数据，如果可以执行部分同步，只需要把缓冲区的部分数据复制给slave，就能恢复正常复制状态。缓冲区的大小越大，slave离线的时间可以更长，复制缓冲区只有在有slave连接的时候才分配内存。没有slave的一段时间，内存会被释放出来。
- 默认值:1mb。
- 是否可以动态修改:yes。
- 值的范围:内存单位。

### repl-backlog-ttl

- 说明：master没有slave一段时间会释放复制缓冲区的内存，repl-backlog-ttl用来设置该时间长度。单位为秒。
- 默认值:3600
- 是否可以动态修改:yes。
- 值的范围:大于0。

### slave-priority

- 说明：当master不可用，Sentinel会根据slave的优先级选举一个master。最低的优先级的slave，当选master。而配置成0，永远不会被选举。
- 默认值:100。
- 是否可以动态修改:yes。
- 值的范围:大于等于0。

### min-slaves-to-write

- 说明：redis提供了可以让master停止写入的方式，如果配置了min-slaves-to-write，健康的slave的个数小于N，mater就禁止写入。master最少得有多少个good的slave存活才能执行写命令。这个配置虽然不能保证N个slave都一定能接收到master的写操作，但是能避免没有足够good slave的时候，master不能写入来避免数据丢失。
- 默认值:0
- 是否可以动态修改:yes。
- 值的范围:大于等于0。

### min-slaves-max-lag

- 说明：延迟小于min-slaves-max-lag的slave才认为是good slave。
- 默认值:10
- 是否可以动态修改:yes。
- 值的范围:大于等于0。

## 安全相关配置

### requirepass

- 说明：requirepass配置可以让用户使用AUTH命令来认证密码，才能使用其他命令。这让redis可以使用在不受信任的网络中。为了保持向后的兼容性，可以注释该命令，因为大部分用户也不需要认证。使用requirepass的时候需要注意，因为redis太快了，每秒可以认证15w次密码，简单的密码很容易被攻破，所以最好使用一个更复杂的密码。
- 默认值:空。
- 是否可以动态修改:yes。
- 值的范围:任意字符串。

### rename-command

- 说明：在共享的模式下，可以把危险的命令给修改成其他名称。比如CONFIG命令可以重命名为一个很难被猜到的命令，这样用户不能使用，而内部工具还能接着使用。
- 默认值:空。
- 是否可以动态修改:no。
- 值的范围:redis命令。

## 一些限制参数

### maxclients

- 说明：设置能连上redis的最大客户端连接数量。默认是10000个客户端连接。由于redis不区分连接是客户端连接还是内部打开文件或者和slave连接等，所以maxclients最小建议设置到32。如果超过了maxclients，redis会给新的连接发送’max number of clients reached’，并关闭连接。
- 默认值:10000。
- 是否可以动态修改:yes。
- 值的范围:大于0。

### maxmemory

- 说明：redis配置的最大内存容量。为0表示没有内存上限。在操作系统为32位的时候，会设置最大内存为3G，同时maxmemory-policy设置为noeviction。需要注意的是，slave的输出缓冲区是不计算在maxmemory内的。所以为了防止主机内存使用完，建议设置的maxmemory需要更小一些。
- 默认值:空。
- 是否可以动态修改:yes。
- 值的范围:内存单位。

### maxmemory-policy

- 说明：内存容量超过maxmemory后的处理策略。volatile-lru：利用LRU算法移除设置过过期时间的key。volatile-random：随机移除设置过过期时间的key。volatile-ttl：移除即将过期的key。allkeys-lru：利用LRU算法移除任何key。allkeys-random：随机移除任何key。noeviction：不移除任何key，只是返回一个写错误。上面的这些驱逐策略，如果redis没有合适的key驱逐，对于写命令，还是会返回错误。写命令包括：set setnx setex append incr decr rpush lpush rpushx lpushx linsert lset rpoplpush sadd sinter sinterstore sunion sunionstore sdiff sdiffstore zadd zincrby zunionstore zinterstore hset hsetnx hmset hincrby incrby decrby getset mset msetnx exec sort。
- 默认值:volatile-lru。
- 是否可以动态修改:yes。
- 值的范围:volatile-lru|allkeys-lru|volatile-random|allkeys-random|volatile-ttl|noeviction。

### maxmemory-samples

- 说明：lru检测的样本数。使用lru或者ttl淘汰算法，从需要淘汰的列表中随机选择sample个key，选出闲置时间最长的key移除。
- 默认值:3。
- 是否可以动态修改:yes。
- 值的范围:大于0。

## aof配置

### appendonly

- 说明：默认redis使用的是rdb方式持久化，这种方式在许多应用中已经足够用了。但是redis如果中途宕机，会导致可能有几分钟的数据丢失。Append Only File是另一种持久化方式，可以提供更好的持久化特性。实例使用默认的fsync方式，redis在主机宕机的时候，仅仅丢失1秒的数据，而只是redis宕机，可能只会丢失一条写入数据。
- 默认值:no。
- 是否可以动态修改:yes。
- 值的范围:yes|no。

### appendfilename

- 说明：aof文件名。
- 默认值:”appendonly.aof”
- 是否可以动态修改:no。
- 值的范围:文件路径。

### appendfsync

- 说明：aof持久化策略的配置，no表示不执行fsync，由操作系统保证数据同步到磁盘，速度最快。always表示每次写入都执行fsync，以保证数据同步到磁盘。everysec表示每秒执行一次fsync，可能会导致丢失这1s数据。更多的细节可以看redis作者的博客：<http://antirez.com/post/redis-persistence-demystified.html>
- 默认值:everysec。
- 是否可以动态修改:yes。
- 值的范围:everysec|always|no。

### no-appendfsync-on-rewrite

- 说明：在aof重写或者写入rdb文件的时候，会执行大量IO，此时对于everysec和always的aof模式来说，执行fsync会造成阻塞过长时间，no-appendfsync-on-rewrite字段设置为默认设置为no。如果对延迟要求很高的应用，这个字段可以设置为yes，否则还是设置为no，这样对持久化特性来说这是更安全的选择。选择no，意味着在子进程save的时候，fsync的策略相当于no。Linux的默认fsync策略是30秒。可能丢失30秒数据。
- 默认值:no。
- 是否可以动态修改:yes。
- 值的范围:yes|no。

### auto-aof-rewrite-percentage

- 说明：aof自动重写配置。当aof文件增长到一定大小的时候Redis能够调用 bgrewriteaof 对日志文件进行重写。Redis会记住上次进行些日志后文件的大小。基础大小会同现在的大小进行比较。如果现在的大小比基础大小大制定的百分比，重写功能将启动。同时需要指定一个最小大小用于aof重写，这个用于阻止即使文件很小但是增长幅度很大也去重写aof文件的情况。
- 默认值:100
- 是否可以动态修改:yes。
- 值的范围:大于0。

### auto-aof-rewrite-min-size

- 说明：aof自动重写配置。需要同时满足aof文件大小大于auto-aof-rewrite-min-size和增长率大于auto-aof-rewrite-percentage才能重写。
- 默认值:64mb。
- 是否可以动态修改:yes。
- 值的范围:内存单位。

### aof-load-truncated

- 说明：aof文件可能在尾部是不完整的，当redis启动的时候，aof文件的数据被载入内存。这重启可能发生在redis所在的宿主机操作系统宕机后，尤其在ext4文件系统没有加上data=ordered选项（redis宕机或者异常终止不会造成尾部不完整现象。）出现这种现象，可以选择让redis退出，或者导入尽可能多的数据。如果选择的是yes，当截断的aof文件被导入的时候，会自动发布一个log给客户端然后load。如果是no，用户必须手动redis-check-aof修复AOF文件才可以。
- 默认值:yes。
- 是否可以动态修改:yes。
- 值的范围:yes|no

## LUA脚本配置

### lua-time-limit

- 说明：如果达到最大时间限制（毫秒），redis会记个log，然后返回error。当一个脚本超过了最大时限。只有SCRIPT KILL和SHUTDOWN NOSAVE可以用。第一个可以杀没有调write命令的东西。要是已经调用了write，只能用第二个命令杀。
- 默认值:5000。
- 是否可以动态修改:yes。
- 值的范围:大于等于0。

## SLOW LOG配置

slog log是用来记录redis运行中执行比较慢的命令耗时。当命令的执行超过了指定时间，就记录在slow log中，slog log保存在内存中，所以没有IO操作。

### slowlog-log-slower-than

- 说明：执行时间比slowlog-log-slower-than大的请求记录到slowlog里面，单位是微秒
- 默认值:10000
- 是否可以动态修改:yes。
- 值的范围:大于等于0.

### slowlog-max-len

- 说明：记录slowlog的最大长度
- 默认值:128
- 是否可以动态修改:yes。
- 值的范围:大于等于0。

## 延迟监控配置

### latency-monitor-threshold

- 说明：延迟监控功能是用来监控redis中执行比较缓慢的一些操作，用LATENCY打印redis实例在跑命令时的耗时图表。只记录大于等于下边设置的值的操作。0的话，就是关闭监视。默认延迟监控功能是关闭的，如果你需要打开，可以通过CONFIG SET命令动态设置。
- 默认值:0。
- 是否可以动态修改:yes。
- 值的范围:大于等于0。

## 事件通知配置

### notify-keyspace-events

- 说明：键空间通知使得客户端可以通过订阅频道或模式，来接收那些以某种方式改动了 Redis 数据集的事件。
  因为开启键空间通知功能需要消耗一些 CPU ，所以在默认配置下，该功能处于关闭状态。
  notify-keyspace-events 的参数可以是以下字符的任意组合，它指定了服务器该发送哪些类型的通知：
  字符 发送的通知

  - K 键空间通知，所有通知以 __keyspace@__ 为前缀
  - E 键事件通知，所有通知以 __keyevent@__ 为前缀
  - g DEL 、 EXPIRE 、 RENAME 等类型无关的通用命令的通知
  - $ 字符串命令的通知
  - l 列表命令的通知
  - s 集合命令的通知
  - h 哈希命令的通知
  - z 有序集合命令的通知
  - x 过期事件：每当有过期键被删除时发送
  - e 驱逐(evict)事件：每当有键因为 maxmemory 政策而被删除时发送
  - A 参数 g$lshzxe 的别名

  输入的参数中至少要有一个 K 或者 E，否则的话，不管其余的参数是什么，都不会有任何 通知被分发。详细使用可以参考<http://redis.io/topics/notifications>

- 默认值:””

- 是否可以动态修改:yes。

- 值的范围:KEg$lshzxeA

## 高级配置

### hash-max-ziplist-entries

- 说明：数据量小于等于hash-max-ziplist-entries的用ziplist，大于hash-max-ziplist-entries用hash。
- 默认值:512。
- 是否可以动态修改:yes。
- 值的范围:大于等于0。

### hash-max-ziplist-value

- 说明：value大小小于等于hash-max-ziplist-value的用ziplist，大于hash-max-ziplist-value用hash。
- 默认值:64。
- 是否可以动态修改:yes。
- 值的范围:大于等于0。

### list-max-ziplist-entries

- 说明：数据量小于等于list-max-ziplist-entries用ziplist，大于list-max-ziplist-entries用list。
- 默认值:512。
- 是否可以动态修改:yes。
- 值的范围:大于等于0。

### list-max-ziplist-value

- 说明：value大小小于等于list-max-ziplist-value的用ziplist，大于list-max-ziplist-value用list。
- 默认值:64。
- 是否可以动态修改:yes。
- 值的范围:大于等于0。

### set-max-intset-entries

- 说明：数据量小于等于set-max-intset-entries用iniset，大于set-max-intset-entries用set。
- 默认值:512。
- 是否可以动态修改:yes。
- 值的范围:大于等于0。

### zset-max-ziplist-entries

- 说明：数据量小于等于zset-max-ziplist-entries用ziplist，大于zset-max-ziplist-entries用zset。
- 默认值:128。
- 是否可以动态修改:yes。
- 值的范围:大于等于0。

### zset-max-ziplist-value

- 说明：value大小小于等于zset-max-ziplist-value用ziplist，大于zset-max-ziplist-value用zset。
- 默认值:64。
- 是否可以动态修改:yes。
- 值的范围:大于等于0。

### hll-sparse-max-bytes

- 说明：value大小小于等于hll-sparse-max-bytes使用稀疏数据结构（sparse），大于hll-sparse-max-bytes使用稠密的数据结构（dense）。一个比16000大的value是几乎没用的，建议的value大概为3000。如果对CPU要求不高，对空间要求较高的，建议设置到10000左右。
- 默认值:3000。
- 是否可以动态修改:yes。
- 值的范围:大于等于0。

### activerehashing

- 说明：Redis将在每100毫秒时使用1毫秒的CPU时间来对redis的hash表进行重新hash，可以降低内存的使用。当你的使用场景中，有非常严格的实时性需要，不能够接受Redis时不时的对请求有2毫秒的延迟的话，把这项配置为no。如果没有这么严格的实时性要求，可以设置为yes，以便能够尽可能快的释放内存。
- 默认值:yes。
- 是否可以动态修改:no。
- 值的范围:yes|no。

### client-output-buffer-limit

- 说明：client output buffer限制，可以用来强制关闭传输缓慢的客户端。格式为client-output-buffer-limit 。class可以为normal、slave、pubsub。hard limit表示output buffer超过该值就直接关闭客户端。soft limit和soft seconds表示output buffer超过soft limit后只需soft seconds后关闭客户端连接。
- 默认值:
  - client-output-buffer-limit normal 0 0 0
  - client-output-buffer-limit slave 256mb 64mb 60
  - client-output-buffer-limit pubsub 32mb 8mb 60
- 是否可以动态修改:yes。
- 值的范围:[normal|slave|pubsub] \d+ \d+ \d+

### hz

- 说明：redis执行任务的频率为1s除以hz。
- 默认值:10。
- 是否可以动态修改:yes。
- 值的范围:1-500。

### aof-rewrite-incremental-fsync

- 说明：在aof重写的时候，如果打开了aof-rewrite-incremental-fsync开关，系统会每32MB执行一次fsync。这对于把文件写入磁盘是有帮助的，可以避免过大的延迟峰值。
- 默认值:yes。
- 是否可以动态修改:yes。
- 值的范围:yes|no。

## 配置项说明

```bash
#redis.conf
# Redis configuration file example.
# ./redis-server /path/to/redis.conf

################################## INCLUDES ###################################
#这在你有标准配置模板但是每个redis服务器又需要个性设置的时候很有用。
# include /path/to/local.conf
# include /path/to/other.conf

################################ GENERAL #####################################

#是否在后台执行，yes：后台运行；no：不是后台运行（老版本默认）
daemonize yes

  #3.2里的参数，是否开启保护模式，默认开启。要是配置里没有指定bind和密码。开启该参数后，redis只会本地进行访问，拒绝外部访问。要是开启了密码   和bind，可以开启。否   则最好关闭，设置为no。
  protected-mode yes
#redis的进程文件
pidfile /var/run/redis/redis-server.pid

#redis监听的端口号。
port 6379

#此参数确定了TCP连接中已完成队列(完成三次握手之后)的长度， 当然此值必须不大于Linux系统定义的/proc/sys/net/core/somaxconn值，默认是511，而Linux的默认参数值是128。当系统并发量大并且客户端速度缓慢的时候，可以将这二个参数一起参考设定。该内核参数默认值一般是128，对于负载很大的服务程序来说大大的不够。一般会将它修改为2048或者更大。在/etc/sysctl.conf中添加:net.core.somaxconn = 2048，然后在终端中执行sysctl -p。
tcp-backlog 511

#指定 redis 只接收来自于该 IP 地址的请求，如果不进行设置，那么将处理所有请求
bind 127.0.0.1

#配置unix socket来让redis支持监听本地连接。
# unixsocket /var/run/redis/redis.sock
#配置unix socket使用文件的权限
# unixsocketperm 700

# 此参数为设置客户端空闲超过timeout，服务端会断开连接，为0则服务端不会主动断开连接，不能小于0。
timeout 0

#tcp keepalive参数。如果设置不为0，就使用配置tcp的SO_KEEPALIVE值，使用keepalive有两个好处:检测挂掉的对端。降低中间设备出问题而导致网络看似连接却已经与对端端口的问题。在Linux内核中，设置了keepalive，redis会定时给对端发送ack。检测到对端关闭需要两倍的设置值。
tcp-keepalive 0

#指定了服务端日志的级别。级别包括：debug（很多信息，方便开发、测试），verbose（许多有用的信息，但是没有debug级别信息多），notice（适当的日志级别，适合生产环境），warn（只有非常重要的信息）
loglevel notice

#指定了记录日志的文件。空字符串的话，日志会打印到标准输出设备。后台运行的redis标准输出是/dev/null。
logfile /var/log/redis/redis-server.log

#是否打开记录syslog功能
# syslog-enabled no

#syslog的标识符。
# syslog-ident redis

#日志的来源、设备
# syslog-facility local0

#数据库的数量，默认使用的数据库是DB 0。可以通过”SELECT “命令选择一个db
databases 16

################################ SNAPSHOTTING ################################
# 快照配置
# 注释掉“save”这一行配置项就可以让保存数据库功能失效
# 设置sedis进行数据库镜像的频率。
# 900秒（15分钟）内至少1个key值改变（则进行数据库保存--持久化） 
# 300秒（5分钟）内至少10个key值改变（则进行数据库保存--持久化） 
# 60秒（1分钟）内至少10000个key值改变（则进行数据库保存--持久化）
save 900 1
save 300 10
save 60 10000

#当RDB持久化出现错误后，是否依然进行继续进行工作，yes：不能进行工作，no：可以继续进行工作，可以通过info中的rdb_last_bgsave_status了解RDB持久化是否有错误
stop-writes-on-bgsave-error yes

#使用压缩rdb文件，rdb文件压缩使用LZF压缩算法，yes：压缩，但是需要一些cpu的消耗。no：不压缩，需要更多的磁盘空间
rdbcompression yes

#是否校验rdb文件。从rdb格式的第五个版本开始，在rdb文件的末尾会带上CRC64的校验和。这跟有利于文件的容错性，但是在保存rdb文件的时候，会有大概10%的性能损耗，所以如果你追求高性能，可以关闭该配置。
rdbchecksum yes

#rdb文件的名称
dbfilename dump.rdb

#数据目录，数据库的写入会在这个目录。rdb、aof文件也会写在这个目录
dir /var/lib/redis

################################# REPLICATION #################################
#复制选项，slave复制对应的master。
# slaveof <masterip> <masterport>

#如果master设置了requirepass，那么slave要连上master，需要有master的密码才行。masterauth就是用来配置master的密码，这样可以在连上master后进行认证。
# masterauth <master-password>

#当从库同主机失去连接或者复制正在进行，从机库有两种运行方式：1) 如果slave-serve-stale-data设置为yes(默认设置)，从库会继续响应客户端的请求。2) 如果slave-serve-stale-data设置为no，除去INFO和SLAVOF命令之外的任何请求都会返回一个错误”SYNC with master in progress”。
slave-serve-stale-data yes

#作为从服务器，默认情况下是只读的（yes），可以修改成NO，用于写（不建议）。
slave-read-only yes

#是否使用socket方式复制数据。目前redis复制提供两种方式，disk和socket。如果新的slave连上来或者重连的slave无法部分同步，就会执行全量同步，master会生成rdb文件。有2种方式：disk方式是master创建一个新的进程把rdb文件保存到磁盘，再把磁盘上的rdb文件传递给slave。socket是master创建一个新的进程，直接把rdb文件以socket的方式发给slave。disk方式的时候，当一个rdb保存的过程中，多个slave都能共享这个rdb文件。socket的方式就的一个个slave顺序复制。在磁盘速度缓慢，网速快的情况下推荐用socket方式。
repl-diskless-sync no

#diskless复制的延迟时间，防止设置为0。一旦复制开始，节点不会再接收新slave的复制请求直到下一个rdb传输。所以最好等待一段时间，等更多的slave连上来。
repl-diskless-sync-delay 5

#slave根据指定的时间间隔向服务器发送ping请求。时间间隔可以通过 repl_ping_slave_period 来设置，默认10秒。
# repl-ping-slave-period 10

#复制连接超时时间。master和slave都有超时时间的设置。master检测到slave上次发送的时间超过repl-timeout，即认为slave离线，清除该slave信息。slave检测到上次和master交互的时间超过repl-timeout，则认为master离线。需要注意的是repl-timeout需要设置一个比repl-ping-slave-period更大的值，不然会经常检测到超时。
# repl-timeout 60

#是否禁止复制tcp链接的tcp nodelay参数，可传递yes或者no。默认是no，即使用tcp nodelay。如果master设置了yes来禁止tcp nodelay设置，在把数据复制给slave的时候，会减少包的数量和更小的网络带宽。但是这也可能带来数据的延迟。默认我们推荐更小的延迟，但是在数据量传输很大的场景下，建议选择yes。
repl-disable-tcp-nodelay no

#复制缓冲区大小，这是一个环形复制缓冲区，用来保存最新复制的命令。这样在slave离线的时候，不需要完全复制master的数据，如果可以执行部分同步，只需要把缓冲区的部分数据复制给slave，就能恢复正常复制状态。缓冲区的大小越大，slave离线的时间可以更长，复制缓冲区只有在有slave连接的时候才分配内存。没有slave的一段时间，内存会被释放出来，默认1m。
# repl-backlog-size 5mb

#master没有slave一段时间会释放复制缓冲区的内存，repl-backlog-ttl用来设置该时间长度。单位为秒。
# repl-backlog-ttl 3600

#当master不可用，Sentinel会根据slave的优先级选举一个master。最低的优先级的slave，当选master。而配置成0，永远不会被选举。
slave-priority 100

#redis提供了可以让master停止写入的方式，如果配置了min-slaves-to-write，健康的slave的个数小于N，mater就禁止写入。master最少得有多少个健康的slave存活才能执行写命令。这个配置虽然不能保证N个slave都一定能接收到master的写操作，但是能避免没有足够健康的slave的时候，master不能写入来避免数据丢失。设置为0是关闭该功能。
# min-slaves-to-write 3

#延迟小于min-slaves-max-lag秒的slave才认为是健康的slave。
# min-slaves-max-lag 10

# 设置1或另一个设置为0禁用这个特性。
# Setting one or the other to 0 disables the feature.
# By default min-slaves-to-write is set to 0 (feature disabled) and
# min-slaves-max-lag is set to 10.

################################## SECURITY ###################################
#requirepass配置可以让用户使用AUTH命令来认证密码，才能使用其他命令。这让redis可以使用在不受信任的网络中。为了保持向后的兼容性，可以注释该命令，因为大部分用户也不需要认证。使用requirepass的时候需要注意，因为redis太快了，每秒可以认证15w次密码，简单的密码很容易被攻破，所以最好使用一个更复杂的密码。
# requirepass foobared

#把危险的命令给修改成其他名称。比如CONFIG命令可以重命名为一个很难被猜到的命令，这样用户不能使用，而内部工具还能接着使用。
# rename-command CONFIG b840fc02d524045429941cc15f59e41cb7be6c52

#设置成一个空的值，可以禁止一个命令
# rename-command CONFIG ""
################################### LIMITS ####################################

# 设置能连上redis的最大客户端连接数量。默认是10000个客户端连接。由于redis不区分连接是客户端连接还是内部打开文件或者和slave连接等，所以maxclients最小建议设置到32。如果超过了maxclients，redis会给新的连接发送’max number of clients reached’，并关闭连接。
# maxclients 10000

#redis配置的最大内存容量。当内存满了，需要配合maxmemory-policy策略进行处理。注意slave的输出缓冲区是不计算在maxmemory内的。所以为了防止主机内存使用完，建议设置的maxmemory需要更小一些。
# maxmemory <bytes>

#内存容量超过maxmemory后的处理策略。
#volatile-lru：利用LRU算法移除设置过过期时间的key。
#volatile-random：随机移除设置过过期时间的key。
#volatile-ttl：移除即将过期的key，根据最近过期时间来删除（辅以TTL）
#allkeys-lru：利用LRU算法移除任何key。
#allkeys-random：随机移除任何key。
#noeviction：不移除任何key，只是返回一个写错误。
#上面的这些驱逐策略，如果redis没有合适的key驱逐，对于写命令，还是会返回错误。redis将不再接收写请求，只接收get请求。写命令包括：set setnx setex append incr decr rpush lpush rpushx lpushx linsert lset rpoplpush sadd sinter sinterstore sunion sunionstore sdiff sdiffstore zadd zincrby zunionstore zinterstore hset hsetnx hmset hincrby incrby decrby getset mset msetnx exec sort。
# maxmemory-policy noeviction

#lru检测的样本数。使用lru或者ttl淘汰算法，从需要淘汰的列表中随机选择sample个key，选出闲置时间最长的key移除。
# maxmemory-samples 5

############################## APPEND ONLY MODE ###############################
#默认redis使用的是rdb方式持久化，这种方式在许多应用中已经足够用了。但是redis如果中途宕机，会导致可能有几分钟的数据丢失，根据save来策略进行持久化，Append Only File是另一种持久化方式，可以提供更好的持久化特性。Redis会把每次写入的数据在接收后都写入 appendonly.aof 文件，每次启动时Redis都会先把这个文件的数据读入内存里，先忽略RDB文件。
appendonly no

#aof文件名
appendfilename "appendonly.aof"

#aof持久化策略的配置
#no表示不执行fsync，由操作系统保证数据同步到磁盘，速度最快。
#always表示每次写入都执行fsync，以保证数据同步到磁盘。
#everysec表示每秒执行一次fsync，可能会导致丢失这1s数据。
appendfsync everysec

# 在aof重写或者写入rdb文件的时候，会执行大量IO，此时对于everysec和always的aof模式来说，执行fsync会造成阻塞过长时间，no-appendfsync-on-rewrite字段设置为默认设置为no。如果对延迟要求很高的应用，这个字段可以设置为yes，否则还是设置为no，这样对持久化特性来说这是更安全的选择。设置为yes表示rewrite期间对新写操作不fsync,暂时存在内存中,等rewrite完成后再写入，默认为no，建议yes。Linux的默认fsync策略是30秒。可能丢失30秒数据。
no-appendfsync-on-rewrite no

#aof自动重写配置。当目前aof文件大小超过上一次重写的aof文件大小的百分之多少进行重写，即当aof文件增长到一定大小的时候Redis能够调用bgrewriteaof对日志文件进行重写。当前AOF文件大小是上次日志重写得到AOF文件大小的二倍（设置为100）时，自动启动新的日志重写过程。
auto-aof-rewrite-percentage 100
#设置允许重写的最小aof文件大小，避免了达到约定百分比但尺寸仍然很小的情况还要重写
auto-aof-rewrite-min-size 64mb

#aof文件可能在尾部是不完整的，当redis启动的时候，aof文件的数据被载入内存。重启可能发生在redis所在的主机操作系统宕机后，尤其在ext4文件系统没有加上data=ordered选项（redis宕机或者异常终止不会造成尾部不完整现象。）出现这种现象，可以选择让redis退出，或者导入尽可能多的数据。如果选择的是yes，当截断的aof文件被导入的时候，会自动发布一个log给客户端然后load。如果是no，用户必须手动redis-check-aof修复AOF文件才可以。
aof-load-truncated yes

################################ LUA SCRIPTING ###############################
# 如果达到最大时间限制（毫秒），redis会记个log，然后返回error。当一个脚本超过了最大时限。只有SCRIPT KILL和SHUTDOWN NOSAVE可以用。第一个可以杀没有调write命令的东西。要是已经调用了write，只能用第二个命令杀。
lua-time-limit 5000

################################ REDIS CLUSTER ###############################
#集群开关，默认是不开启集群模式。
# cluster-enabled yes

#集群配置文件的名称，每个节点都有一个集群相关的配置文件，持久化保存集群的信息。这个文件并不需要手动配置，这个配置文件有Redis生成并更新，每个Redis集群节点需要一个单独的配置文件，请确保与实例运行的系统中配置文件名称不冲突
# cluster-config-file nodes-6379.conf

#节点互连超时的阀值。集群节点超时毫秒数
# cluster-node-timeout 15000

  #在进行故障转移的时候，全部slave都会请求申请为master，但是有些slave可能与master断开连接一段时间了，导致数据过于陈旧，这样的slave不应该被提升为master。该参数就是用来判>断slave节点与master断线的时间是否过长。判断方法是：
  #比较slave断开连接的时间和(node-timeout * slave-validity-factor) + repl-ping-slave-period
  #如果节点超时时间为三十秒, 并且slave-validity-factor为10,假设默认的repl-ping-slave-period是10秒，即如果超过310秒slave将不会尝试进行故障转移 
  #可能出现由于某主节点失联却没有从节点能顶上的情况，从而导致集群不能正常工作，在这种情况下，只有等到原来的主节点重新回归到集群，集群才恢复运作
  #如果设置成０，则无论从节点与主节点失联多久，从节点都会尝试升级成主节
  # cluster-slave-validity-factor 10
  #master的slave数量大于该值，slave才能迁移到其他孤立master上，如这个参数若被设为2，那么只有当一个主节点拥有2 个可工作的从节点时，它的一个从节点会尝试迁移。
  #主节点需要的最小从节点数，只有达到这个数，主节点失败时，它从节点才会进行迁移。
  # cluster-migration-barrier 1
  #默认情况下，集群全部的slot有节点分配，集群状态才为ok，才能提供服务。设置为no，可以在slot没有全部分配的时候提供服务。不建议打开该配置，这样会造成分区的时候，小分区的mster一直在接受写请求，而造成很长时间数据不一致。
  #在部分key所在的节点不可用时，如果此参数设置为”yes”(默认值), 则整个集群停止接受操作；如果此参数设置为”no”，则集群依然为可达节点上的key提供读操作
  # cluster-require-full-coverage yes
################################## SLOW LOG ###################################
###slog log是用来记录redis运行中执行比较慢的命令耗时。当命令的执行超过了指定时间，就记录在slow log中，slog log保存在内存中，所以没有IO操作。
#执行时间比slowlog-log-slower-than大的请求记录到slowlog里面，单位是微秒，所以1000000就是1秒。注意，负数时间会禁用慢查询日志，而0则会强制记录所有命令。
slowlog-log-slower-than 10000

#慢查询日志长度。当一个新的命令被写进日志的时候，最老的那个记录会被删掉。这个长度没有限制。只要有足够的内存就行。你可以通过 SLOWLOG RESET 来释放内存。
slowlog-max-len 128

################################ LATENCY MONITOR ##############################
#延迟监控功能是用来监控redis中执行比较缓慢的一些操作，用LATENCY打印redis实例在跑命令时的耗时图表。只记录大于等于下边设置的值的操作。0的话，就是关闭监视。默认延迟监控功能是关闭的，如果你需要打开，也可以通过CONFIG SET命令动态设置。
latency-monitor-threshold 0

############################# EVENT NOTIFICATION ##############################
#键空间通知使得客户端可以通过订阅频道或模式，来接收那些以某种方式改动了 Redis 数据集的事件。因为开启键空间通知功能需要消耗一些 CPU ，所以在默认配置下，该功能处于关闭状态。
#notify-keyspace-events 的参数可以是以下字符的任意组合，它指定了服务器该发送哪些类型的通知：
##K 键空间通知，所有通知以 __keyspace@__ 为前缀
##E 键事件通知，所有通知以 __keyevent@__ 为前缀
##g DEL 、 EXPIRE 、 RENAME 等类型无关的通用命令的通知
##$ 字符串命令的通知
##l 列表命令的通知
##s 集合命令的通知
##h 哈希命令的通知
##z 有序集合命令的通知
##x 过期事件：每当有过期键被删除时发送
##e 驱逐(evict)事件：每当有键因为 maxmemory 政策而被删除时发送
##A 参数 g$lshzxe 的别名
#输入的参数中至少要有一个 K 或者 E，否则的话，不管其余的参数是什么，都不会有任何 通知被分发。详细使用可以参考http://redis.io/topics/notifications

notify-keyspace-events ""

############################### ADVANCED CONFIG ###############################
#数据量小于等于hash-max-ziplist-entries的用ziplist，大于hash-max-ziplist-entries用hash
hash-max-ziplist-entries 512
#value大小小于等于hash-max-ziplist-value的用ziplist，大于hash-max-ziplist-value用hash。
hash-max-ziplist-value 64

#数据量小于等于list-max-ziplist-entries用ziplist，大于list-max-ziplist-entries用list。
list-max-ziplist-entries 512
#value大小小于等于list-max-ziplist-value的用ziplist，大于list-max-ziplist-value用list。
list-max-ziplist-value 64

#数据量小于等于set-max-intset-entries用iniset，大于set-max-intset-entries用set。
set-max-intset-entries 512

#数据量小于等于zset-max-ziplist-entries用ziplist，大于zset-max-ziplist-entries用zset。
zset-max-ziplist-entries 128
#value大小小于等于zset-max-ziplist-value用ziplist，大于zset-max-ziplist-value用zset。
zset-max-ziplist-value 64

#value大小小于等于hll-sparse-max-bytes使用稀疏数据结构（sparse），大于hll-sparse-max-bytes使用稠密的数据结构（dense）。一个比16000大的value是几乎没用的，建议的value大概为3000。如果对CPU要求不高，对空间要求较高的，建议设置到10000左右。
hll-sparse-max-bytes 3000

#Redis将在每100毫秒时使用1毫秒的CPU时间来对redis的hash表进行重新hash，可以降低内存的使用。当你的使用场景中，有非常严格的实时性需要，不能够接受Redis时不时的对请求有2毫秒的延迟的话，把这项配置为no。如果没有这么严格的实时性要求，可以设置为yes，以便能够尽可能快的释放内存。
activerehashing yes

##对客户端输出缓冲进行限制可以强迫那些不从服务器读取数据的客户端断开连接，用来强制关闭传输缓慢的客户端。
#对于normal client，第一个0表示取消hard limit，第二个0和第三个0表示取消soft limit，normal client默认取消限制，因为如果没有寻问，他们是不会接收数据的。
client-output-buffer-limit normal 0 0 0
#对于slave client和MONITER client，如果client-output-buffer一旦超过256mb，又或者超过64mb持续60秒，那么服务器就会立即断开客户端连接。
client-output-buffer-limit slave 256mb 64mb 60
#对于pubsub client，如果client-output-buffer一旦超过32mb，又或者超过8mb持续60秒，那么服务器就会立即断开客户端连接。
client-output-buffer-limit pubsub 32mb 8mb 60

#redis执行任务的频率为1s除以hz。
hz 10

#在aof重写的时候，如果打开了aof-rewrite-incremental-fsync开关，系统会每32MB执行一次fsync。这对于把文件写入磁盘是有帮助的，可以避免过大的延迟峰值。
aof-rewrite-incremental-fsync yes
```

## **sentinel的设置**（sentinel.conf）说明：

```bash
#####
#####    sentinel 配置说明
#####

#Sentinel实例的端口号
port 26379

#Sentinel 实例的目录
dir /tmp

#日志文件
logfile /var/log/redis/redis-server.log

#后台执行
daemonize yes

#3.2里的参数，是否开启保护模式，默认开启。要是配置里没有指定bind和密码。开启该参数后，redis只会本地进行访问，拒绝外部访问。要是开启了密码   和bind，可以开启。否   则最好关闭，设置为no。
protected-mode no

#格式：sentinel <option_name> <master_name> <option_value>；这一行代表sentinel监控的master的名字叫做mymaster,地址为127.0.0.1:6379，行尾最后的一个2代表什么意思呢？我们知道，网络是不可靠的，有时候一个sentinel会因为网络堵塞而误以为一个master redis已经死掉了，当sentinel集群式，解决这个问题的方法就变得很简单，只需要多个sentinel互相沟通来确认某个master是否真的死了，这个2代表，当集群中有2个sentinel认为master死了时，才能真正认为该master已经不可用了。
sentinel monitor mymaster 127.0.0.1 6379 2

#sentinel会向master发送心跳PING来确认master是否存活，如果master在“一定时间范围”内不回应PONG 或者是回复了一个错误消息，那么这个sentinel会主观地(单方面地)认为这个master已经不可用了(subjectively down, 也简称为SDOWN)。而这个down-after-milliseconds就是用来指定这个“一定时间范围”的，默认单位是毫秒，默认30秒。
sentinel down-after-milliseconds mymaster 30000

#在发生failover主备切换时，这个选项指定了最多可以有多少个slave同时对新的master进行同步，这个数字越小，完成failover所需的时间就越长，但是如果这个数字越大，就意味着越多的slave因为replication而不可用。可以通过将这个值设为 1 来保证每次只有一个slave处于不能处理命令请求的状态。
sentinel parallel-syncs mymaster 1

#failover过期时间，当failover开始后，在此时间内仍然没有触发任何failover操作，当前sentinel将会认为此次failoer失败。默认180秒，即3minutes.
sentinel failover-timeout mymaster 180000

#设置连master和slaves验证密码，在监控redis实例时很有用
#sentinel auth-pass mymaster xxxxxxx

#发生切换之后执行的一个自定义脚本：如发邮件、vip切换等
#sentinel notification-script <master-name> <script-path>
#发生切换之后执行的一个自定义脚本：如发邮件、vip切换等
#sentinel client-reconfig-script T1 /opt/bin/notify.py
```

## **redis配置模板：**

```bash
#redis.conf
# Redis configuration file example.
# ./redis-server /path/to/redis.conf
daemonize yes
pidfile /var/run/redis/redis-server.pid
port 6379
tcp-backlog 511
#bind 127.0.0.1
unixsocket /var/run/redis/redis.sock
#unixsocketperm 700

timeout 0
tcp-keepalive 0

loglevel notice
logfile /var/log/redis/redis-server.log

# syslog-enabled no
# syslog-ident redis
# syslog-facility local0

databases 16

################################ SNAPSHOTTING  ################################
save 900 1
save 300 10
save 60 10000

stop-writes-on-bgsave-error yes
rdbcompression yes
rdbchecksum yes

dbfilename dump.rdb
dir /var/lib/redis

################################# REPLICATION #################################
# slaveof <masterip> <masterport>
# masterauth <master-password>

slave-serve-stale-data yes
slave-read-only yes

repl-diskless-sync no
repl-diskless-sync-delay 5

# repl-ping-slave-period 10
# repl-timeout 60

repl-disable-tcp-nodelay no
repl-backlog-size 5mb
repl-backlog-ttl 3600

slave-priority 100

#min-slaves-to-write 3
# min-slaves-max-lag 10

################################## SECURITY ###################################
# requirepass foobared
# rename-command CONFIG b840fc02d524045429941cc15f59e41cb7be6c52
# rename-command CONFIG ""
################################### LIMITS ####################################
maxclients 10000
#maxmemory <bytes>
maxmemory-policy noeviction
# maxmemory-samples 5

############################## APPEND ONLY MODE ###############################
appendonly yes
appendfilename "appendonly.aof"
appendfsync everysec

no-appendfsync-on-rewrite no
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb
aof-load-truncated yes

################################ LUA SCRIPTING  ###############################
lua-time-limit 5000

################################ REDIS CLUSTER  ###############################
# cluster-enabled yes
# cluster-config-file nodes-6379.conf
# cluster-node-timeout 15000
# cluster-slave-validity-factor 10
# cluster-migration-barrier 1
# cluster-require-full-coverage yes

################################## SLOW LOG ###################################
slowlog-log-slower-than 10000
slowlog-max-len 128

################################ LATENCY MONITOR ##############################
latency-monitor-threshold 0

############################# EVENT NOTIFICATION ##############################
notify-keyspace-events ""

############################### ADVANCED CONFIG ###############################
hash-max-ziplist-entries 512
hash-max-ziplist-value 64

list-max-ziplist-entries 512
list-max-ziplist-value 64

set-max-intset-entries 512

zset-max-ziplist-entries 128
zset-max-ziplist-value 64

hll-sparse-max-bytes 3000

activerehashing yes

client-output-buffer-limit normal 0 0 0
client-output-buffer-limit slave 256mb 64mb 60
client-output-buffer-limit pubsub 32mb 8mb 60

hz 10
aof-rewrite-incremental-fsync yes
```

## **redis sentinel配置模板：** 

```bash
port 20086      #默认端口26379

dir "/tmp"

logfile "/var/log/redis/sentinel_20086.log"

daemonize yes

#3.2设置  
protected-mode no 
#格式：sentinel <option_name> <master_name> <option_value>；#该行的意思是：监控的master的名字叫做T1（自定义）,地址为127.0.0.1:10086，行尾最后的一个2代表在sentinel集群中，多少个sentinel认为masters死了，才能真正认为该master不可用了。
sentinel monitor T1 127.0.0.1 10086 2
#sentinel会向master发送心跳PING来确认master是否存活，如果master在“一定时间范围”内不回应PONG 或者是回复了一个错误消息，那么这个sentinel会主观地(单方面地)认为这个master已经不可用了(subjectively down, 也简称为SDOWN)。而这个down-after-milliseconds就是用来指定这个“一定时间范围”的，单位是毫秒，默认30秒。
sentinel down-after-milliseconds T1 15000
#failover过期时间，当failover开始后，在此时间内仍然没有触发任何failover操作，当前sentinel将会认为此次failoer失败。默认180秒，即3分钟。
sentinel failover-timeout T1 120000
#在发生failover主备切换时，这个选项指定了最多可以有多少个slave同时对新的master进行同步，这个数字越小，完成failover所需的时间就越长，但是如果这个数字越大，就意味着越多的slave因为replication而不可用。可以通过将这个值设为 1 来保证每次只有一个slave处于不能处理命令请求的状态。
sentinel parallel-syncs T1 1
#sentinel 连接设置了密码的主和从
#sentinel auth-pass <master_name> xxxxx

#发生切换之后执行的一个自定义脚本：如发邮件、vip切换等
#sentinel notification-script <master-name> <script-path>
#sentinel client-reconfig-script <master-name> <script-path>
```

