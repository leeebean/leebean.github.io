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


# 一、Redis的安装
以mac为例
#### 1.下载地址
[download](https://redis.io/download)
#### 2.解压
    tar zxvf redis-6.0.9.tar.gz
#### 3.安装
    cd redis-6.0.9
    sudo make test
    sudo make install


# 二、Redis的常见命令

### 1.基础命令

详细请参考 [Redis 命令参考](http://doc.redisfans.com/)

#### 1.1启动

`redis-server`
![启动成功](https://i.loli.net/2020/12/16/fDS9EQcPOavzWsV.png)

默认端口号为`6379`
#### 1.2打开命令行
`redis-cli`

![存储成功](https://i.loli.net/2020/12/16/lbTjQZgDF5iVsHX.png)


#### 1.3退出

`exit`

    127.0.0.1:6379> exit
    ➜    ~

## 2.Key(健)


#### 删除&检查
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

#### 序列化和反序列化

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









