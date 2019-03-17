# Redis 学习记录 | 文档


## 简介

Redis 文档记录，参考官网文档。

Redis 是一个开源（BSD许可）的，内存中的数据结构存储系统，它可以用作数据库、缓存和消息中间件。 它支持多种类型的数据结构，如 字符串（strings）， 散列（hashes）， 列表（lists）， 集合（sets）， 有序集合（sorted sets） 与范围查询， bitmaps， hyperloglogs 和 地理空间（geospatial） 索引半径查询。 Redis 内置了 复制（replication），LUA脚本（Lua scripting）， LRU驱动事件（LRU eviction），事务（transactions） 和不同级别的 磁盘持久化（persistence）， 并通过 Redis哨兵（Sentinel）和自动 分区（Cluster）提供高可用性（high availability）。

同时拥有丰富的支持主流语言的 客户端，C、C++、Python、Erlang、R、C#、Java、PHP、Objective- C、Perl、Ruby、Scala、Go、JavaScript 等。

## Redis 的特点

1.数据结构丰富

Redis 虽然也是键值对数据库，但是和 Memcached 不同的是， Redis 支持多种类型的数据结构，不仅可以是字符串，同时还提供散 列(hashes)，列表(lists)，集合(sets)，有序集合(sorted sets) 等数据结构。

2.数据持久化

Redis 支持数据的持久化，可以将内存中的数据保存在磁盘中，重启的时候可以再次加载使用。

3.数据备份

Redis 支持数据的备份，也就是 master-slave 模式的数据备份。

## Redis 安装

安装编译环境：

```
yum -y install gcc tcl -y
```

下载并编译安装

```
$ wget http://download.redis.io/releases/redis-5.0.3.tar.gz
$ tar xzf redis-5.0.3.tar.gz
$ cd redis-5.0.3
$ make
```
此时编译成功，可以使用了。此时会在 src 目录下生成可执行程序，如果想安装到本机，可以继续执行 `make install` 命令, 默认安装在 `/usr/local/bin` 目录下。

拿到源码包里面的 redis.conf 文件，备份一份出来，进行修改，首先把运行模式改成`后台运行`模式。 找到如下配置，改成 yes

```
# By default Redis does not run as a daemon. Use 'yes' if you need it.
# Note that Redis will write a pid file in /var/run/redis.pid when daemonized.
# daemonize no 默认的
daemonize yes 
```
如果不指定 ip 地址的话，只能本地连接，要想开启远程连接，需要手动加上ip，就像下面这样：

```
bind 127.0.0.1 192.168.33.11
```

启动 redis 服务，启动的时候指定配置文件的位置。eg:

```
redis-server ~/conf/redis.conf

```
启动之后(后台运行)，打印出如下日志信息:

```
5722:C 21 Jan 2019 14:33:26.832 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
5722:C 21 Jan 2019 14:33:26.832 # Redis version=5.0.3, bits=64, commit=00000000, modified=0, pid=5722, just started
5722:C 21 Jan 2019 14:33:26.832 # Configuration loaded
```

可以使用命令 ps 查看 redis 的进程:

```
ps -ef | grep redis
```

## Redis 使用

Redis 的 key 是二进制安全的，这意味着可以使用任何二进制为key。

切换数据库：

## Redis 持久化

前面也提到了 Redis 支持不同级别的持久化。

如下:

- RDB
- AOF
- RDB && AOF
- 关闭持久化

下面依次介绍,

1.
RDB （Redis DataBase）在指定时间段内把内存中的数据集快照写入磁盘，恢复时直接读取快照文件加载到内存。大致的过程是这样的，Redis 会 fork 一个子线程来做持久化的任务，它会先将数据写到一个临时文件中，等待持久化结束之后，再用这个临时文件替换掉上一次持久化的文件。上述过程中，主进程是不进行任何 IO 操作的，所以持久化性能极高。

fork 的作用是复制一个与主进程一样的子进程，这个新的子进程的数据（变量、环境变量、程序计数器等）全部一致。保存之后的家文件叫做 dump.rdb，这是一个很紧凑的文件，它保存了某个时间点得数据集,非常适用于数据集的备份,比如你可以在每个小时报保存一下过去24小时内的数据,同时每天保存过去30天的数据,这样即使出了问题你也可以根据需求恢复到不同版本的数据集。

但是 RDB 也有优缺点：可能会丢失最后一次持久化的数据。如果对数据的完整性很敏感的话，建议使用 AOF

redis 在该模式下持久化的原则是：1分钟内修改1万次，5分钟内修改10次，15分钟内改动一次。

关闭的话，使用命令 `conf set save " "` 或者修改 redis.conf 文件。

2.

AOF(Append Only File) 以日志的形式记录每次写操作，日志文件只允许追加，不允许修改。Redis 启动之初，会读取该文件，重构数据。换句话说就是 Redis 在启动之后，会把文件中的写记录从头开始执行一遍恢复数据。

开启 AOF 是修改 redis.conf 文件：

```
############################## APPEND ONLY MODE ###############################

# By default Redis asynchronously dumps the dataset on disk. This mode is
# good enough in many applications, but an issue with the Redis process or
# a power outage may result into a few minutes of writes lost (depending on
# the configured save points).
#
# The Append Only File is an alternative persistence mode that provides
# much better durability. For instance using the default data fsync policy
# (see later in the config file) Redis can lose just one second of writes in a
# dramatic event like a server power outage, or a single write if something
# wrong with the Redis process itself happens, but the operating system is
# still running correctly.
#
# AOF and RDB persistence can be enabled at the same time without problems.
# If the AOF is enabled on startup Redis will load the AOF, that is the file
# with the better durability guarantees.
#
# Please check http://redis.io/topics/persistence for more information.

# 默认是 no 改成 yes 开启 AOF

appendonly yes

```

关于 AOF 的策略，官方是这样说的：


```
# If unsure, use "everysec".

# appendfsync always 数据完整性较好，但是性能差。
appendfsync everysec 默认推荐这个，异步操作。
# appendfsync no 从不
```

AOF 是追加的形式，所以文件会越来越大，为了避免文件太大，引入了重写机制。aof 文件大小超过一定的阈值，redis 会自动的压缩文件，可以使用命令 `bgrewroteaof` 。

重写的原理是:aof 文件越来越大的时候，会 fork 一个新的线程将文件重写，遍历新进程里面的数据，每条记录有一天 set 语句，写到新的文件，最后覆盖之前的文件。

如何触发呢？ redis 会记录上次重写的 aof 文件的大小，当 现在 aof 的大小为上次的一杯且大小超过 64 M。关于这个，配置文件里面有说明的，可以修改：

```
# This is how it works: Redis remembers the size of the AOF file after the
# latest rewrite (if no rewrite has happened since the restart, the size of
# the AOF at startup is used).
#
# This base size is compared to the current size. If the current size is
# bigger than the specified percentage, the rewrite is triggered. Also
# you need to specify a minimal size for the AOF file to be rewritten, this
# is useful to avoid rewriting the AOF file even if the percentage increase
# is reached but it is still pretty small.
#
# Specify a percentage of zero in order to disable the automatic AOF
# rewrite feature.

auto-aof-rewrite-percentage 100

auto-aof-rewrite-min-size 64mb

```
AOF 的优点：

使用 AOF 持久化会让 Redis 变得非常耐久:你可以设置不同的 fsync 策 略，比如无 fsync ，每秒钟一次 fsync ，或者每次执行写入命令时 fsync 。 AOF 的默认策略为每秒钟 fsync 一次，在这种配置下，Redis 仍然可以保持良 好的性能，并且就算发生故障停机，也最多只会丢失一秒钟的数据( fsync 会 在后台线程执行，所以主线程可以继续努力地处理命令请求)。

AOF 文件是一个只进行追加操作的日志文件(append only log)， 因此对 AOF 文件的写入不需要进行 seek ， 即使日志因为某些原因而包含了未写入 完整的命令(比如写入时磁盘已满，写入中途停机，等等)， `redis-check-aof` 工具也可以轻易地修复这种问题。

Redis 可以在 AOF 文件体积变得过大时，自动地在后台对 AOF 进行重 写: 重写后的新 AOF 文件包含了恢复当前数据集所需的最小命令集合。 整 个重写操作是绝对安全的，因为 Redis 在创建新 AOF 文件的过程中，会继续 将命令追加到现有的 AOF 文件里面，即使重写过程中发生停机，现有的 AOF 文件也不会丢失。 而一旦新 AOF 文件创建完毕，Redis 就会从旧 AOF 文 件切换到新 AOF 文件，并开始对新 AOF 文件进行追加操作。

AOF 文件有序地保存了对数据库执行的所有写入操作， 这些写入操作以 Redis 协议的格式保存， 因此 AOF 文件的内容非常容易被人读懂， 对文件 进行分析(parse)也很轻松。 导出(export) AOF 文件也非常简单: 举个 例子， 如果你不小心执行了 FLUSHALL 命令， 但只要 AOF 文件未被重写， 那么只要停止服务器， 移除 AOF 文件末尾的 FLUSHALL 命令， 并重启 Redis ， 就可以将数据集恢复到 FLUSHALL 执行之前的状态。


备份 Redis 数据。

Redis 对于数据备份是非常友好的， 因为你可以在服务器运行的时候对 RDB 文件进行复制: RDB 文件一旦被创建， 就不会进行任何修改。 当服 务器要创建一个新的 RDB 文件时， 它先将文件的内容保存在一个临时文件里 面， 当临时文件写入完毕时， 程序才使用 rename(2) 原子地用临时文件替换 原来的 RDB 文件。
这也就是说， 无论何时， 复制 RDB 文件都是绝对安全的。建议:创建一个定期任务(cron job)， 每小时将一个 RDB 文件备份到一个文件夹， 并且每天将一个 RDB 文件备份到另一个文件夹。 确保快照的备份都带有相应的日期和时间信息， 每次执行定期任务脚本时， 使用 find 命令来删除过期的快照: 比如说， 你可以保留最近 48 小时 内的每小时快照， 还可以保留最近一两个月的每日快照。至少每天一次， 将 RDB 备份到你的数据中心之外， 或者至少是备份到你 运行 Redis 服务器的物理机器之外。





