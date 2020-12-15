---
title: Redis（一）概述
date: 2020-12-15 14:59:00
tags: [Redis,理论知识]
categories: [中间件,Redis,Nosql]
keywords: Java
description: 重温基础，查缺补漏
top_img:
comments: true
cover: https://i.loli.net/2020/12/15/AFq8SCP2jefbHKa.png
toc:
toc_number:
auto_open:
copyright:
mathjax:
katex:
aplayer:
highlight_shrink:
top: false
---
# Redis
## 一、什么是Redis
> Redis（Remote Dictionary Server )，即远程字典服务，是一个开源的使用ANSI C语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。从2010年3月15日起，Redis的开发工作由VMware主持。从2013年5月开始，Redis的开发由Pivotal赞助

redis 是c语言开发的一个开源的高性能简直对（key-value）的内存数据库，可以用同作数据库、缓存、消息中间件等。他是一种NoSql(not-only sql,泛之非关系型数据库)的数据库

## 二、Redis有哪些特点
1. 性能优秀，数据在内存中，读写速度非常快，支持并发 10W QPS。
2. 单进程单线程，是线程安全的，采用 IO 多路复用机制。
3. 丰富的数据类型，支持字符串（strings）、散列（hashes）、列表（lists）、集合（sets）、有序集合（sorted sets）等。
4. 支持数据持久化。可以将内存中数据保存在磁盘中，重启时加载。
5. 主从复制，哨兵，高可用。可以用作分布式锁。可以作为消息中间件使用，支持发布订阅。

## 三、Redis支持的五种数据类型

+ string(字符串)
+ hash(散列表)
+ list(列表)
+ set(集合)
+ z-set(有序集合)

![redistObject](https://i.loli.net/2020/12/15/poqLKbetAuUCkYz.png)

![encoding](https://i.loli.net/2020/12/15/8N52qAZJ49h6pXP.png)

### 1.String字符串

String是Redis最基本的数据类型，它数据结构存储方式有三种int、raw、embstr，它不仅可以存储字符串，还可以存储整数、浮点数、甚至是二进制数据，比如jpg图片或序列化的对象（一个String最大存储**512M**，String 类型是二进制安全的。

### 2.hash散列表

![hash](https://i.loli.net/2020/12/15/GrCuH2vKhaZyoWd.png)

Hash（哈希）类型是从redis-2.0版本之后才有的数据结构，用于存放字符串和字符串之间的映射。Hash对象的实现方式有两种，分别是 Ziplist 和 hashtable，其中 hashtable 的存储方式 Key 是 String 类型， value 也是以 key-value 的形式进行存储。has适合存储对象。

`常用命令`：hget，hset，hgetall 等。

### 3.list列表

List 类型是一种链表结构，主要功能是 push （进栈）、pop（出栈）；List 类型在 redis-3.2 之前的版本是使用 ziplist 和 linkedlist 进行实现的，之后版本就是引入 quicklist

`应用场景`：List 应用场景非常多，也是 Redis 最重要的数据结构之一，比如 Twitter 的关注列表，粉丝列表都可以用 List 结构来实现。

`常用命令`：lpush、rpush、lpop、rpop、lrange（获取列表片段）等。



### 4.set集合

Set 类型是 String 类型的无序集合，Set 集合是不可重复的，而 List 列表可以存储相同的字符串

`应用场景`：Redis Set 对外提供的功能和 List 一样是一个列表，特殊之处在于 Set 是自动去重的，而且 Set 提供了判断某个成员是否在一个 Set 集合中。

`实现方式`：Redis List 的是实现是一个双向链表，既可以支持反向查找和遍历，更方便操作，不过带来了额外的内存开销。

`常用命令`：sdd、spop、smembers、sunion 等

### 5.zset有序集合

Zset 类型是 String 类型的有序集合，Zset 集合是不可重复的，它的底层实现是 ziplist 和 skiplist（跳跃表）

`使用场景`：Sorted Set 可以通过用户额外提供一个优先级（score）的参数来为成员排序，并且是插入有序的，即自动排序。

`实现方式`：Redis Sorted Set 的内部使用 HashMap 和跳跃表（skipList）来保证数据的存储和有序，HashMap 里放的是成员到 Score 的映射。

`常用命令`：zadd、zrange、zrem、zcard 等。

### 总结
类型|**简介**|**特性**|**场景**|**大小**
:---|:---|:---|:---|:---
**string**|二进制安全|可以包含任何数据，例如jpg图片或序列化对象|-|最大512m
**hash**|健值对集合，map|适合存储对象|存储、读取、修改用户属性|-
**List**|双向链表|增删快，提供了操作某一元素的api|消息通知、消息队列|-
**set**|hash表实现元素不重复|增、删、查的复杂度都是O(1),提供了交集、并集、差集的操作|共同好友|-
**zset**|set元素增加一个权重score，按权重排序|数据插入时就做好了排序|排行榜，带权重的消息队列|-

## 四、如何使用Redis
`结合Springboot`
1.  RedisTemplate
2.  Spring Cache

内容待展开

## 五、Redis为什么这么快
> 官方提供的数据可以达到 100000+ 的 QPS

+ 大家都在用肯定很快
+ 单线程
    Redis 是单进程单线程的模型，因为 Redis 完全是基于内存的操作，CPU 不是 Redis 的瓶颈，Redis 的瓶颈最有可能是机器内存的大小或者网络带宽。

    1. Redis 完全基于内存，绝大部分请求是纯粹的内存操作，非常迅速，数据存在内存中，类似于 HashMap，HashMap 的优势就是查找和操作的时间复杂度是 O(1).
    2. 数据结构简单，对数据操作也简单.
    3. 采用单线程，避免了不必要的上下文切换和竞争条件，不存在多线程导致的 CPU 切换，不用去考虑各种锁的问题，不存在加锁释放锁操作，没有死锁问题导致的性能消耗。
    4. 使用多路复用 IO 模型，非阻塞 IO。

## 六、为什么选用redis而不是memcached

比较方面|**redis**|**memcached**
:---|:---|:---
**存储方式**|Redis 有部分数据存在硬盘上，这样能保证数据的持久性|会把数据全部存在内存之中，断电后会挂掉，数据不能超过内存大小
**数据类型支持**|支持五种数据类型|对数据类型的支持简单，只支持简单的 key-value
**底层模型**|直接自己构建了 VM 机制，因为一般的系统调用系统函数的话，会浪费一定的时间去移动和请求|-
**value大小**|max 1GB| max 1MB

## 七、淘汰策略
策略|描述
:---|:---
volitile-lru|从已设置过期时间的kv集中，优先对近期最少使用的淘汰（less recently used）
volitile-ttl|从已设置过期时间的kv集中，优先对剩余时间短的淘汰（time to live）
volitile-random|从已设置过期时间的kv集中，随机淘汰
volitile-lfu| 从已设置过期时间的kv集中，讲访问频率最小的淘汰（least frequency use）
allkeys-lru|从所有kv集中，优先对近期最少使用的淘汰（less recently used）
allkey-random|从所有kv集中，随机淘汰
allkeys-lfu|从所有kv集中，讲访问频率最小的淘汰（least frequency use）
noeviction|不淘汰策略，若超过最大内存则反悔错误信息

## 八、持久化










