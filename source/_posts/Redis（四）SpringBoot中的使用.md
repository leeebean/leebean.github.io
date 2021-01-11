---
title: Redis（四）SpringBoot中的使用
date: 2020-12-18 14:59:00
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

```
        String key = "key";
        String value = "abcde";
        ValueOperations<String, String> valueOperations = redisTemplate.opsForValue();
        // set
        valueOperations.set(key, value);
        // set设置过期时间
        valueOperations.set(key, value, 1000, TimeUnit.SECONDS);
        // 重新设置key 如果存在则返回false 否则返回true
        System.out.println(valueOperations.setIfAbsent(key,value));
        // set设置偏移量
        valueOperations.set(key, value, 2);
        // 将二进制第offset位值变为value
        //System.out.println(valueOperations.setBit(key, 1, true));
        // 对key所储存的字符串值，获取指定偏移量上的位(bit)
        //System.out.println(valueOperations.getBit(key,10));
        // 获取key值 ababcde
        System.out.println(valueOperations.get(key));
```

代码|操作|key|value|返回值|描述
:---:|:---:|:---:|:---:|:---:|:---
5|valueOperations.set(key, value)|key|abdce|void|存入key value
7|valueOperations.set(key, value, 1000, TimeUnit.SECONDS)|key|abcde|void|存入带过期时间的key value
9|valueOperations.setIfAbsent(key,value)|key|abcde|boolean(false)|存在则更新value 返回false 不存在则存入返回true
11|valueOperations.set(key, value, 2)|key|ababcde|void|将key的value值从第二个字符开始替换为value
17|valueOperations.get(key)|key|ababcde|String(ababcde)|获取存入redis的key对应的value值


```
        // 截取key的子字符串 abab
        System.out.println(valueOperations.get(key, 0, 3));
        // 获取指定key的字符串的长度 7
        System.out.println(valueOperations.size(key));
        // 设置key跟value的值，同时获取key的值(原来的值) ababcde
        System.out.println(valueOperations.getAndSet(key, value));
        // 获取原来的key的值后在后面新增上新的字符串 并返回更新后的字符串长度
        System.out.println(valueOperations.append(key, "fg"));
        // abcdefg
        System.out.println(valueOperations.get(key));

```

代码|操作|key|value|返回值|描述
:---:|:---:|:---:|:---:|:---:|:---
2|valueOperations.get(key, 0, 3)|key|abdce|String(abab)|截取key的0-3位的子字符串
4|valueOperations.size(key)|key|abcde|Long(7)|获取指定key的字符串（value）的长度 
6|valueOperations.getAndSet(key, value)|key|abcde|String(ababcde)|设置key跟value的值，同时获取key的值(原来的值) 
8|valueOperations.append(key, "fg")|key|abcdefg|Integr(7)|获取原来的key的值后在后面新增上新的字符串(fg) 并返回更新后的字符串长度

```
        String key2 = "length";
        String key3 = "height";
        Long length = 20L;
        Double height = 182.5d;
        List<String> keys = Arrays.asList(new String[]{key,key2,key3});
        // 增量方式存储long值和double值（正值则自增，负值则自减） 并返回的值
        System.out.println(valueOperations.increment(key2, length));
        System.out.println(valueOperations.increment(key3, height));
        System.out.println(valueOperations.multiGet(keys));
        System.out.println(valueOperations.increment(key2, length));
        System.out.println(valueOperations.increment(key3, height));
        // 根据keys批量获取值
        System.out.println(valueOperations.multiGet(keys));
```


代码|操作|key|value|返回值|描述
:---:|:---:|:---:|:---:|:---:|:---
7|valueOperations.increment(key2, length)|key2|20|Long(20)|增量方式存储long值 并返回值
8|valueOperations.increment(key3, height)|key3|182.5|Double(182.5)|增量方式存储double值 并返回值
9|valueOperations.multiGet(keys)|keys[key,length,height]|[abcdefg, 20, 182.5]|List<V> [abcdefg, 20, 182.5]|批量获取value 没有则返回null
10|valueOperations.increment(key2, length)|key2|40|Long(40)|增量方式存储long值 并返回值
11|valueOperations.increment(key3, height)|key3|365.0|Double(365.0)|增量方式存储double值 并返回值
13|valueOperations.multiGet(keys)|keys[key,length,height]|[abcdefg, 40, 365]|List<V> [abcdefg, 40, 365]|批量获取value 没有则返回null

```
        redisTemplate.delete(keys);
        Map<String,String> map = new HashMap<>();
        map.put(key,value);
        map.put(key2,length.toString());
        map.put(key3,height.toString());
        // 增加map集合到redis
        valueOperations.multiSet(map);
        // 不存在即新增map的操作返回true 存在返回false
        System.out.println(valueOperations.multiSetIfAbsent(map));
```

代码|操作|key|value|返回值|描述
:---:|:---:|:---:|:---:|:---:|:---
1|redisTemplate.delete(keys)|-|-|Long(3)|批量删除 返回删除的key的数量
6|valueOperations.multiSet(map);|keys[key,length,height]|[abcdefg, 20, 182.5]|void|增加map集合到redis
9|valueOperations.multiGet(keys)|keys[key,length,height]|[abcdefg, 20, 182.5]|Boolean(false)|不存在即新增map


## hash

```
        HashOperations<String, String, Object> valueOperations = redisTemplate.opsForHash();
        Map<String, Object> map = new HashMap();
        User user = new User("张三", 1, 20);
        map.put("user1", user);
        // 以map集合的形式添加键值对
        valueOperations.putAll("user:", map);
        User user2 = new User("李四", 2, 21);
        // 新增map值
        valueOperations.put("user:", "user2", user2);
        // 如果存在返回false
        System.out.println(valueOperations.putIfAbsent("user:", "user2", user2));
        List<String> keys = Arrays.asList(new String[]{"user1", "user2","user3"});
        // filed数
        System.out.println(valueOperations.size("user:"));
        // 所有的 hk
        System.out.println(valueOperations.keys("user:"));
        // 所有的键值对
        System.out.println(valueOperations.entries("user:"));
        ScanOptions options = new ScanOptions.ScanOptionsBuilder().count(1).match("*").build();
        // 匹配的键值对
        System.out.println(valueOperations.scan("user:",options));
        // 所有的值
        System.out.println(valueOperations.values("user:"));
        // 批量获取hv
        System.out.println(valueOperations.multiGet("user:",keys));
        // 批量删除
        System.out.println(valueOperations.delete("user:","user3","user2"));
        // 是否存在
        System.out.println(valueOperations.hasKey("user:","user1"));
        // 增量put Double值
        valueOperations.increment("user:","height",182.5D);
        // 增量put Long值
        valueOperations.increment("user:","length",20L);
        valueOperations.increment("user:","height",0.5D);
        valueOperations.increment("user:","length",1L);
        System.out.println(valueOperations.entries("user:"));
```
console
```
        false
        2
        [user1, user2]
        {user1=User(name=张三, sex=1, age=20), user2=User(name=李四, sex=2, age=21)}
        org.springframework.data.redis.core.ConvertingCursor@15639d09
        [User(name=张三, sex=1, age=20), User(name=李四, sex=2, age=21)]
        [User(name=张三, sex=1, age=20), User(name=李四, sex=2, age=21), null]
        1
        true
        {length=21, user1=User(name=张三, sex=1, age=20), height=183}
```


代码|操作|h|hk|hv
:---:|:---:|:---:|:---:|:---:
6|valueOperations.putAll("user:", map);|user:|user1|{user1=User(name=张三, sex=1, age=20)}
9|valueOperations.put("user:", "user2", user2);|user:|[user1,user2]|{user1=User(name=张三, sex=1, age=20), user2=User(name=李四, sex=2, age=21)}
11|valueOperations.putIfAbsent("user:", "user2", user2));|user:|[user1,user2]|{user1=User(name=张三, sex=1, age=20), user2=User(name=李四, sex=2, age=21)}
27|valueOperations.delete("user:","user3","user2");|user:|user1|{user1=User(name=张三, sex=1, age=20)}
31,<br>33,<br>34,<br>35|valueOperations.increment("user:","height",182.5D);<br>valueOperations.increment("user:","length",20L);<br>valueOperations.increment("user:","height",0.5D);<br>valueOperations.increment("user:","length",1L);|user:|[length,user1,height]|{length=21,<br> user1=User(name=张三, sex=1, age=20),<br> height=183}

## list

## set

## zset

# 二、注解



