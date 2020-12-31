---
title: Redis（三）Springboot整合Redis
date: 2020-12-17 14:59:00
tags: [Redis,Springboot]
categories: Redis
keywords: Reids,SpringBoot
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


# 一、基础配置

## 1.pom.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.4.1</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.lee</groupId>
    <artifactId>redis-demo1</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>redis-demo1</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>


        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
            <plugin><!--编译跳过测试文件检查的生命周期-->
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <configuration>
                    <skip>true</skip>
                </configuration>
            </plugin>
        </plugins>
        <resources>
            <!--如果pro和xml文件放在源码java包下，也需要编译-->
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.properties</include>
                    <include>**/*.xml</include>
                </includes>
                <filtering>false</filtering>
            </resource>
        </resources>
    </build>

</project>
```

## 2.application.yml

```
spring:
  cache:
    type: redis
  redis:
    database: 2
    host: 127.0.0.1
    port: 6379
    password:
    jedis:
      pool:
        max-active: 8 #连接池最大连接数（使用负值表示没有限制）
        max-wait: -1s #连接池最大阻塞等待时间（使用负值表示没有限制）
        max-idle: 8 #连接池中的最大空闲连接
        min-idle: 0 #连接池中的最小空闲连接
    lettuce:
      shutdown-timeout: 100ms
```

## 3.RedisConfig.java

```
package com.lee.redisdemo1.config;

import com.fasterxml.jackson.annotation.JsonAutoDetect;
import com.fasterxml.jackson.annotation.PropertyAccessor;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.cache.CacheManager;
import org.springframework.cache.annotation.CachingConfigurerSupport;
import org.springframework.cache.interceptor.KeyGenerator;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.cache.RedisCacheManager;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.core.StringRedisTemplate;
import org.springframework.data.redis.serializer.Jackson2JsonRedisSerializer;

import java.lang.reflect.Method;

/**
 * @author lee
 */
@Configuration
public class RedisConfig extends CachingConfigurerSupport {

    /**
     * 缓存对象集合中，缓存是以key-value形式保存的,
     * 当不指定缓存的key时，SpringBoot会使用keyGenerator生成Key。
     */
    @Override
    @Bean
    public KeyGenerator keyGenerator() {
        return new KeyGenerator() {
            @Override
            public Object generate(Object target, Method method, Object... params) {
                StringBuilder sb = new StringBuilder();
                //类名+方法名
                sb.append(target.getClass().getName());
                sb.append(method.getName());
                for (Object obj : params) {
                    sb.append(obj.toString());
                }
                return sb.toString();
            }
        };
    }

    /**
     * 缓存管理器
     */
    @SuppressWarnings("rawtypes")
    @Bean
    public CacheManager cacheManager(RedisConnectionFactory connectionFactory) {
        RedisCacheManager cacheManager = RedisCacheManager.create(connectionFactory);
        return cacheManager;
    }



//    /**
//     * 实例化RedisTemplate对象
//     */
//    @Bean
//    public RedisTemplate<String, String> redisTemplate(RedisConnectionFactory factory) {
//        StringRedisTemplate template = new StringRedisTemplate(factory);
//        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
//        ObjectMapper om = new ObjectMapper();
//        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
//        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
//        jackson2JsonRedisSerializer.setObjectMapper(om);
//        template.setValueSerializer(jackson2JsonRedisSerializer);
//        template.afterPropertiesSet();
//        return template;
//    }

}

```

## 4.RedisController

```
@RestController
public class RedisController {

    @Autowired
    private RedisTemplate redisTemplate;

    @GetMapping("hello")
    public String hello() {
        String name = (String) redisTemplate.opsForValue().get("name");
        if (!StringUtils.hasText(name)) {
            name = getName();
            redisTemplate.opsForValue().set("name", name);
        }
        return "hello " + name;
    }

    private String getName() {
        return "lee";
    }
}
```

# 二、reidsTemplate和stringRedisTemplate

## 1.关系不同

`ReidsTemplate` **继承** `StringRedisTemplate<String,String>`

## 2.数据不共通,各自存取

```

@SpringBootTest
public class RedisControllerTest {

    @Autowired
    private RedisTemplate redisTemplate;

    @Autowired
    private StringRedisTemplate stringRedisTemplate;

    @Test
    void templateTest() {
        redisTemplate.opsForValue().set("redisTemplate","hello");
        stringRedisTemplate.opsForValue().set("stringRedisTemplate","hi");
        System.out.println(redisTemplate.opsForValue().get("redisTemplate"));
        System.out.println(redisTemplate.opsForValue().get("stringRedisTemplate"));
        System.out.println(stringRedisTemplate.opsForValue().get("redisTemplate"));
        System.out.println(stringRedisTemplate.opsForValue().get("stringRedisTemplate"));
    }
}
```

输出结果：

    hello
    null
    null
    hi

## 3.可读性不同


**序列化相关知识：[日积月累-Java的序列化](https://leeebean.github.io/leebean.github.io/2020/12/23/%E6%97%A5%E7%A7%AF%E6%9C%88%E7%B4%AF-Java%E7%9A%84%E5%BA%8F%E5%88%97%E5%8C%96/)**


User
```
@Data
public class User implements Externalizable {

    public User() {
        System.out.println("调用构造方法！");
    }

    private static final long serialVersionUID = -1266182246612973862L;
    private String name;
    private Integer sex;
    private Integer age;


    @Override
    public void writeExternal(ObjectOutput out) throws IOException {
        //将name反转后写入二进制流
        StringBuffer reverse = new StringBuffer(name).reverse();
        System.out.println(reverse.toString());
        out.writeObject(reverse);
        out.writeInt(age);
    }

    @Override
    public void readExternal(ObjectInput in) throws IOException, ClassNotFoundException {
        //将读取的字符串反转后赋值给name实例变量
        this.name = ((StringBuffer) in.readObject()).reverse().toString();
        System.out.println(name);
        this.age = in.readInt();
    }

}

```

Test
```
        User user = new User();
        user.setName("lee");
        user.setSex(1);
        user.setAge(20);
        redisTemplate.opsForHash().put("user:", "lee", user);
        stringRedisTemplate.opsForHash().put("user:", "lee", user.toString());
        System.out.println(redisTemplate.opsForHash().get("user:", "lee"));
        System.out.println(stringRedisTemplate.opsForHash().get("user:", "lee"));
```
console

    调用构造方法！
    eel
    调用构造方法！
    lee
    User(name=lee, sex=null, age=20)
    User(name=lee, sex=1, age=20)


**StringReidsTmplate 存放的数据具有良好的可读性**

![StringReidsTmplate](https://i.loli.net/2020/12/31/Xqsd3lxbWcIPYt2.png)

**RedisTemplate存数据时是先将数据序列化成*字节数组*，再存放到Redis数据库中，不具有可读性**

![ReidsTmplate](https://i.loli.net/2020/12/31/TafmCRUSY5u1Wr9.png)


## 4.序列化策略不同

+ RedisTemplate**默认**采用的的是**JDK自带的序列化策略**

+ StringRedisTemplate**默认**采用的是**String的序列化策略**

# 三、序列化策略

+ **GenericToStringSerializer**: 可以将任何对象泛化为字符串并序列化
+ **Jackson2JsonRedisSerializer**: 跟JacksonJsonRedisSerializer实际上是一样的
+ **JacksonJsonRedisSerializer**: 序列化object对象为json字符串
+ **JdkSerializationRedisSerializer**: 序列化java对象
+ **StringRedisSerializer**: 简单的字符串序列化


