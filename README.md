# Redis 学习记录 | 文档

[toc]

## 简介

Redis 文档记录，参考官网文档

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
此时编译成功，可以使用了。



