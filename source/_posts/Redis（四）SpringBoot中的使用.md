---
title: Redis（四）SpringBoot中的使用
date: 2020-12-17 14:59:00
tags: [Redis,Springboot]
categories: Redis
keywords: Reids,SpringBoot,Java
description: redis的使用
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


# 一、RedisTmplate/StringRedisTmplate

## RedisTmplate

```
        ValueOperations<String, String> valueOperations = redisTemplate.opsForValue();
        String key = "name";
        String key2 = "age";
        String key3 = "height";
        String value = "lee";
        Long age = 20L;
        Double height = 181.5D;
        List<String> keys = Arrays.asList(new String[]{key, key2, key3});
        valueOperations.set(key, value);
        valueOperations.increment(key2, age);
        valueOperations.increment(key3, height);
        byte[] nameByte = redisTemplate.dump(key);
        // 序列化key [[B@316cda31]
        System.out.println(Arrays.asList(nameByte));
        // 不会改变原key lee
        System.out.println(valueOperations.get(key));
        // 反序列化
        redisTemplate.restore("name2", redisTemplate.dump(key), 3, TimeUnit.HOURS);
        // 重命名
        redisTemplate.rename("name2", "name3");
        // 如果new key 不存在则重命名
        redisTemplate.renameIfAbsent("name3","age");
        // 有哪些key [name, name3, height, age]
        System.err.println(redisTemplate.keys("*"));
        // 获取类型 STRING
        System.err.println(redisTemplate.type(key));
        // 获取一个随机的key值
        System.err.println(redisTemplate.randomKey());
        // 获取当前key的剩下的过期时间 1 表示未设置过期时间
        System.err.println(redisTemplate.getExpire(key));
        // 获取当前key的剩下的过期时间 (时间单位) 2
        System.err.println(redisTemplate.getExpire("name3",TimeUnit.HOURS));
        // 将当前数据库的key移动到指定redis中数据库当中
        redisTemplate.move("name3", 1);
        // application.yml中设置的db0 -null
        System.out.println(valueOperations.get("name3"));
        // 获取当前key的剩下的过期时间 (时间单位) -2表示没有该key值
        System.err.println(redisTemplate.getExpire("name3",TimeUnit.HOURS));
        redisTemplate.persist(key);
        // 删除redis中对应的key值
        redisTemplate.delete(key);
        // 判断redis中是否存在key值 false
        System.err.println(redisTemplate.hasKey(key));
        // 批量删除
        redisTemplate.delete(keys);
        //[null, null, null]
        System.err.println(valueOperations.multiGet(keys));

```
## string

## hash

## list

## set

## zset

# 二、注解



