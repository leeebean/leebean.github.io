---
title: Redis（二）redis的安装和常见命令
date: 2020-12-16 14:59:00
tags: Redis
categories: [中间件,Redis,Nosql]
keywords: Java
description: redis的安装和快速使用
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
[Redis 命令参考](http://doc.redisfans.com/)
# 一、Redis的安装

## 1.下载
[Redis官网下载地址](https://redis.io/download)
## 2.解压
    tar zxvf redis-6.0.9.tar.gz
## 3.安装
    cd redis-6.0.9
    sudo make test
    sudo make install

## 4.测试

### 启动
`redis-server`

![启动成功](https://i.loli.net/2020/12/16/fDS9EQcPOavzWsV.png)
默认端口号为`6379`
### 打开命令行
`redis-cli`

![存储成功](https://i.loli.net/2020/12/16/lbTjQZgDF5iVsHX.png)

# 二、Redis的常见命令

## 1.Connection（连接）

### 切换数据库

`select`

切换到指定的数据库，数据库索引号 index 用数字值指定，以 0 作为起始索引值。
默认使用 0 号数据库。

    127.0.0.1:6379[3]> select 0
    OK
    127.0.0.1:6379> set db_number 0
    OK
    127.0.0.1:6379> get db_number
    "0"
    127.0.0.1:6379> select 2
    OK
    127.0.0.1:6379[2]> get db_number
    (nil)
    127.0.0.1:6379[2]> set db_number 2
    OK
    127.0.0.1:6379[2]> get db_number
    "2"
    127.0.0.1:6379[2]>

### 退出

`quit`
请求服务器关闭与当前客户端的连接。
一旦所有等待中的回复(如果有的话)顺利写入到客户端，连接就会被关闭。

    127.0.0.1:6379[2]> quit
    ➜  ~

### Ping

使用客户端向 Redis 服务器发送一个 PING ，如果服务器运作正常的话，会返回一个 PONG 。
通常用于测试与服务器的连接是否仍然生效，或者用于测量延迟值。

    127.0.0.1:6379> ping
    PONG

### 打印


ECHO message
打印一个特定的信息 message ，测试时使用。

    127.0.0.1:6379> echo 'ni hao'
    "ni hao"

### auth
`auth`
auth password

通过设置配置文件中 requirepass 项的值(使用命令 CONFIG SET requirepass password )，可以使用密码来保护 Redis 服务器。
如果开启了密码保护的话，在每次连接 Redis 服务器之后，就要使用 AUTH 命令解锁，解锁之后才能使用其他 Redis 命令。
如果 AUTH 命令给定的密码 password 和配置文件中的密码相符的话，服务器会返回 OK 并开始接受命令输入。
另一方面，假如密码不匹配的话，服务器将返回一个错误，并要求客户端需重新输入密码。
>因为 Redis 高性能的特点，在很短时间内尝试猜测非常多个密码是有可能的，因此请确保使用的密码足够复杂和足够长，以免遭受密码猜测攻击。

`config set requirepass 123456`
设置密码

    127.0.0.1:6379> config set requirepass 123456
    OK
    127.0.0.1:6379> quit
    ➜  ~ redis-cli
    127.0.0.1:6379> ping
    (error) NOAUTH Authentication required.
    127.0.0.1:6379> auth 123
    (error) WRONGPASS invalid username-password pair
    127.0.0.1:6379> auth 123456
    OK
    127.0.0.1:6379> ping
    PONG
config set requirepass ''
清除密码

    127.0.0.1:6379> config set requirepass ''
    OK
    127.0.0.1:6379> quit
    ➜  ~ redis-cli
    127.0.0.1:6379> ping
    PONG
## 2.Key(健)
### 删除&检查
`del`
删除给定的一个或多个key，不存在将忽略

`exists`
检查给定 key 是否存在。

set key

    127.0.0.1:6379> set name lee
    OK
    127.0.0.1:6379> set sex male
    OK
    127.0.0.1:6379> set age 25
    OK
检查key是否存在

    127.0.0.1:6379> exists name
    (integer) 1
删除单个key

    127.0.0.1:6379> del name
    (integer) 1
    127.0.0.1:6379> exists name
    (integer) 0
删除不存在的key

    127.0.0.1:6379> del name
    (integer) 0
    127.0.0.1:6379> del phone
    (integer) 0
    127.0.0.1:6379>
删除多个key

    127.0.0.1:6379> del name sex age phone
    (integer) 2
    127.0.0.1:6379> exists sex
    (integer) 0

### 序列化和反序列化

`dump`
序列化给定 key ，并返回被序列化的值，

`restore`
反序列化给定的序列化值，并将它和给定的 key 关联。

序列化

    127.0.0.1:6379> dump name
    "\x00\x03lee\t\x00\t\xa5\x84\xd92T:\x84"
    127.0.0.1:6379> exists age
    (integer) 0
    127.0.0.1:6379> dump age
    (nil)

`restore key ttl serialized-value`
反序列化
参数 ttl 以毫秒为单位为 key 设置生存时间；如果 ttl 为 0 ，那么不设置生存时间。

restore 在执行反序列化之前会先对序列化值的 RDB 版本和数据校验和进行检查，如果 RDB 版本不相同或者数据不完整的话，那么 RESTORE 会拒绝进行反序列化，并返回一个错误。

    127.0.0.1:6379> dump name
    "\x00\x03lee\t\x00\t\xa5\x84\xd92T:\x84"
    127.0.0.1:6379> restore name2 0 "\x00\x03lee\t\x00\t\xa5\x84\xd92T:\x84"
    OK
    127.0.0.1:6379> get name
    "lee"
    127.0.0.1:6379> get name2
    "lee"
    127.0.0.1:6379> set name li
    OK
    127.0.0.1:6379> get name
    "li"
    127.0.0.1:6379> get name2
    "lee"
    127.0.0.1:6379> restore name3 0 "asdadsadasd"
    (error) ERR DUMP payload version or checksum are wrong

