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



//
//    @Bean
//    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
//        // 创建RedisTemplate<String, Object>对象
//        RedisTemplate<String, Object> template = new RedisTemplate<>();
//        // 配置连接工厂
//        template.setConnectionFactory(factory);
//        // 定义Jackson2JsonRedisSerializer序列化对象
//        Jackson2JsonRedisSerializer<Object> jacksonSeial = new Jackson2JsonRedisSerializer<>(Object.class);
//        ObjectMapper om = new ObjectMapper();
//        // 指定要序列化的域，field,get和set,以及修饰符范围，ANY是都有包括private和public
//        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
//        // 指定序列化输入的类型，类必须是非final修饰的，final修饰的类，比如String,Integer等会报异常
//        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
//        jacksonSeial.setObjectMapper(om);
//        StringRedisSerializer stringSerial = new StringRedisSerializer();
//        // redis key 序列化方式使用stringSerial
//        template.setKeySerializer(stringSerial);
//        // redis value 序列化方式使用jackson
//        template.setValueSerializer(jacksonSeial);
//        // redis hash key 序列化方式使用stringSerial
//        template.setHashKeySerializer(stringSerial);
//        // redis hash value 序列化方式使用jackson
//        template.setHashValueSerializer(jacksonSeial);
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

# 三、spring-data-redis序列化策略

+ **GenericToStringSerializer**: 可以将任何对象泛化为字符串并序列化
+ **Jackson2JsonRedisSerializer**: 跟JacksonJsonRedisSerializer实际上是一样的
+ **JacksonJsonRedisSerializer**: 序列化object对象为json字符串
+ **JdkSerializationRedisSerializer**: 序列化java对象
+ **StringRedisSerializer**: 简单的字符串序列化
+ **GenericToStringSerializer**:类似StringRedisSerializer的字符串序列化
+ **GenericJackson2JsonRedisSerializer**:类似Jackson2JsonRedisSerializer，但使用时构造函数不用特定的类参考以上序列化,自定义序列化类;
+ **OxmSerializer**


# 四、Springboot中Redis的坑

## 1.reidsTemplate和stringRedisTemplate混用的坑

### 1.1 现象

RedisConfig
```
    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
        // 创建RedisTemplate<String, Object>对象
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        // 配置连接工厂
        template.setConnectionFactory(factory);
        // 定义Jackson2JsonRedisSerializer序列化对象
        Jackson2JsonRedisSerializer<Object> jacksonSeial = new Jackson2JsonRedisSerializer<>(Object.class);
        ObjectMapper om = new ObjectMapper();
        // 指定要序列化的域，field,get和set,以及修饰符范围，ANY是都有包括private和public
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        // 指定序列化输入的类型，类必须是非final修饰的，final修饰的类，比如String,Integer等会报异常
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jacksonSeial.setObjectMapper(om);
        StringRedisSerializer stringSerial = new StringRedisSerializer();
        // redis key 序列化方式使用stringSerial
        template.setKeySerializer(stringSerial);
        // redis value 序列化方式使用jackson
        template.setValueSerializer(jacksonSeial);
        // redis hash key 序列化方式使用stringSerial
        template.setHashKeySerializer(stringSerial);
        // redis hash value 序列化方式使用jackson
        template.setHashValueSerializer(jacksonSeial);
        template.afterPropertiesSet();
        return template;
    }
```
测试方法
```
    @Test
    void templateTest() {
        User user = new User();
        user.setName("lee");
        user.setSex(1);
        user.setAge(20);
        redisTemplate.opsForHash().put("user:", "lee", user);
        stringRedisTemplate.opsForHash().put("user:", "lee", user.toString());
        System.out.println(stringRedisTemplate.opsForHash().get("user:", "lee"));
        System.out.println(redisTemplate.opsForHash().get("user:", "lee"));
    }
```
console
```


调用构造方法！
User(name=lee, sex=1, age=20)


org.springframework.data.redis.serializer.SerializationException: Could not read JSON: Unrecognized token 'User': was expecting (JSON String, Number, Array, Object or token 'null', 'true' or 'false')
 at [Source: (byte[])"User(name=lee, sex=1, age=20)"; line: 1, column: 6]; nested exception is com.fasterxml.jackson.core.JsonParseException: Unrecognized token 'User': was expecting (JSON String, Number, Array, Object or token 'null', 'true' or 'false')
 at [Source: (byte[])"User(name=lee, sex=1, age=20)"; line: 1, column: 6]

	at org.springframework.data.redis.serializer.Jackson2JsonRedisSerializer.deserialize(Jackson2JsonRedisSerializer.java:75)
	at org.springframework.data.redis.core.AbstractOperations.deserializeHashValue(AbstractOperations.java:355)
	at org.springframework.data.redis.core.DefaultHashOperations.get(DefaultHashOperations.java:55)
	at com.lee.redisdemo.RedisControllerTest.templateTest(RedisControllerTest.java:28)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at org.junit.platform.commons.util.ReflectionUtils.invokeMethod(ReflectionUtils.java:688)
	at org.junit.jupiter.engine.execution.MethodInvocation.proceed(MethodInvocation.java:60)
	at org.junit.jupiter.engine.execution.InvocationInterceptorChain$ValidatingInvocation.proceed(InvocationInterceptorChain.java:131)
	at org.junit.jupiter.engine.extension.TimeoutExtension.intercept(TimeoutExtension.java:149)
	at org.junit.jupiter.engine.extension.TimeoutExtension.interceptTestableMethod(TimeoutExtension.java:140)
	at org.junit.jupiter.engine.extension.TimeoutExtension.interceptTestMethod(TimeoutExtension.java:84)
	at org.junit.jupiter.engine.execution.ExecutableInvoker$ReflectiveInterceptorCall.lambda$ofVoidMethod$0(ExecutableInvoker.java:115)
	at org.junit.jupiter.engine.execution.ExecutableInvoker.lambda$invoke$0(ExecutableInvoker.java:105)
	at org.junit.jupiter.engine.execution.InvocationInterceptorChain$InterceptedInvocation.proceed(InvocationInterceptorChain.java:106)
	at org.junit.jupiter.engine.execution.InvocationInterceptorChain.proceed(InvocationInterceptorChain.java:64)
	at org.junit.jupiter.engine.execution.InvocationInterceptorChain.chainAndInvoke(InvocationInterceptorChain.java:45)
	at org.junit.jupiter.engine.execution.InvocationInterceptorChain.invoke(InvocationInterceptorChain.java:37)
	at org.junit.jupiter.engine.execution.ExecutableInvoker.invoke(ExecutableInvoker.java:104)
	at org.junit.jupiter.engine.execution.ExecutableInvoker.invoke(ExecutableInvoker.java:98)
	at org.junit.jupiter.engine.descriptor.TestMethodTestDescriptor.lambda$invokeTestMethod$6(TestMethodTestDescriptor.java:210)
	at org.junit.platform.engine.support.hierarchical.ThrowableCollector.execute(ThrowableCollector.java:73)
	at org.junit.jupiter.engine.descriptor.TestMethodTestDescriptor.invokeTestMethod(TestMethodTestDescriptor.java:206)
	at org.junit.jupiter.engine.descriptor.TestMethodTestDescriptor.execute(TestMethodTestDescriptor.java:131)
	at org.junit.jupiter.engine.descriptor.TestMethodTestDescriptor.execute(TestMethodTestDescriptor.java:65)
	at org.junit.platform.engine.support.hierarchical.NodeTestTask.lambda$executeRecursively$5(NodeTestTask.java:139)
	at org.junit.platform.engine.support.hierarchical.ThrowableCollector.execute(ThrowableCollector.java:73)
	at org.junit.platform.engine.support.hierarchical.NodeTestTask.lambda$executeRecursively$7(NodeTestTask.java:129)
	at org.junit.platform.engine.support.hierarchical.Node.around(Node.java:137)
	at org.junit.platform.engine.support.hierarchical.NodeTestTask.lambda$executeRecursively$8(NodeTestTask.java:127)
	at org.junit.platform.engine.support.hierarchical.ThrowableCollector.execute(ThrowableCollector.java:73)
	at org.junit.platform.engine.support.hierarchical.NodeTestTask.executeRecursively(NodeTestTask.java:126)
	at org.junit.platform.engine.support.hierarchical.NodeTestTask.execute(NodeTestTask.java:84)
	at java.util.ArrayList.forEach(ArrayList.java:1257)
	at org.junit.platform.engine.support.hierarchical.SameThreadHierarchicalTestExecutorService.invokeAll(SameThreadHierarchicalTestExecutorService.java:38)
	at org.junit.platform.engine.support.hierarchical.NodeTestTask.lambda$executeRecursively$5(NodeTestTask.java:143)
	at org.junit.platform.engine.support.hierarchical.ThrowableCollector.execute(ThrowableCollector.java:73)
	at org.junit.platform.engine.support.hierarchical.NodeTestTask.lambda$executeRecursively$7(NodeTestTask.java:129)
	at org.junit.platform.engine.support.hierarchical.Node.around(Node.java:137)
	at org.junit.platform.engine.support.hierarchical.NodeTestTask.lambda$executeRecursively$8(NodeTestTask.java:127)
	at org.junit.platform.engine.support.hierarchical.ThrowableCollector.execute(ThrowableCollector.java:73)
	at org.junit.platform.engine.support.hierarchical.NodeTestTask.executeRecursively(NodeTestTask.java:126)
	at org.junit.platform.engine.support.hierarchical.NodeTestTask.execute(NodeTestTask.java:84)
	at java.util.ArrayList.forEach(ArrayList.java:1257)
	at org.junit.platform.engine.support.hierarchical.SameThreadHierarchicalTestExecutorService.invokeAll(SameThreadHierarchicalTestExecutorService.java:38)
	at org.junit.platform.engine.support.hierarchical.NodeTestTask.lambda$executeRecursively$5(NodeTestTask.java:143)
	at org.junit.platform.engine.support.hierarchical.ThrowableCollector.execute(ThrowableCollector.java:73)
	at org.junit.platform.engine.support.hierarchical.NodeTestTask.lambda$executeRecursively$7(NodeTestTask.java:129)
	at org.junit.platform.engine.support.hierarchical.Node.around(Node.java:137)
	at org.junit.platform.engine.support.hierarchical.NodeTestTask.lambda$executeRecursively$8(NodeTestTask.java:127)
	at org.junit.platform.engine.support.hierarchical.ThrowableCollector.execute(ThrowableCollector.java:73)
	at org.junit.platform.engine.support.hierarchical.NodeTestTask.executeRecursively(NodeTestTask.java:126)
	at org.junit.platform.engine.support.hierarchical.NodeTestTask.execute(NodeTestTask.java:84)
	at org.junit.platform.engine.support.hierarchical.SameThreadHierarchicalTestExecutorService.submit(SameThreadHierarchicalTestExecutorService.java:32)
	at org.junit.platform.engine.support.hierarchical.HierarchicalTestExecutor.execute(HierarchicalTestExecutor.java:57)
	at org.junit.platform.engine.support.hierarchical.HierarchicalTestEngine.execute(HierarchicalTestEngine.java:51)
	at org.junit.platform.launcher.core.EngineExecutionOrchestrator.execute(EngineExecutionOrchestrator.java:108)
	at org.junit.platform.launcher.core.EngineExecutionOrchestrator.execute(EngineExecutionOrchestrator.java:88)
	at org.junit.platform.launcher.core.EngineExecutionOrchestrator.lambda$execute$0(EngineExecutionOrchestrator.java:54)
	at org.junit.platform.launcher.core.EngineExecutionOrchestrator.withInterceptedStreams(EngineExecutionOrchestrator.java:67)
	at org.junit.platform.launcher.core.EngineExecutionOrchestrator.execute(EngineExecutionOrchestrator.java:52)
	at org.junit.platform.launcher.core.DefaultLauncher.execute(DefaultLauncher.java:96)
	at org.junit.platform.launcher.core.DefaultLauncher.execute(DefaultLauncher.java:75)
	at com.intellij.junit5.JUnit5IdeaTestRunner.startRunnerWithArgs(JUnit5IdeaTestRunner.java:69)
	at com.intellij.rt.junit.IdeaTestRunner$Repeater.startRunnerWithArgs(IdeaTestRunner.java:33)
	at com.intellij.rt.junit.JUnitStarter.prepareStreamsAndStart(JUnitStarter.java:230)
	at com.intellij.rt.junit.JUnitStarter.main(JUnitStarter.java:58)
Caused by: com.fasterxml.jackson.core.JsonParseException: Unrecognized token 'User': was expecting (JSON String, Number, Array, Object or token 'null', 'true' or 'false')
 at [Source: (byte[])"User(name=lee, sex=1, age=20)"; line: 1, column: 6]
	at com.fasterxml.jackson.core.JsonParser._constructError(JsonParser.java:1851)
	at com.fasterxml.jackson.core.base.ParserMinimalBase._reportError(ParserMinimalBase.java:717)
	at com.fasterxml.jackson.core.json.UTF8StreamJsonParser._reportInvalidToken(UTF8StreamJsonParser.java:3588)
	at com.fasterxml.jackson.core.json.UTF8StreamJsonParser._handleUnexpectedValue(UTF8StreamJsonParser.java:2683)
	at com.fasterxml.jackson.core.json.UTF8StreamJsonParser._nextTokenNotInObject(UTF8StreamJsonParser.java:865)
	at com.fasterxml.jackson.core.json.UTF8StreamJsonParser.nextToken(UTF8StreamJsonParser.java:757)
	at com.fasterxml.jackson.databind.ObjectMapper._initForReading(ObjectMapper.java:4664)
	at com.fasterxml.jackson.databind.ObjectMapper._readMapAndClose(ObjectMapper.java:4513)
	at com.fasterxml.jackson.databind.ObjectMapper.readValue(ObjectMapper.java:3572)
	at org.springframework.data.redis.serializer.Jackson2JsonRedisSerializer.deserialize(Jackson2JsonRedisSerializer.java:73)
	... 68 more


2020-1-31 16:36:10.711  INFO 38789 --- [extShutdownHook] o.s.s.concurrent.ThreadPoolTaskExecutor  : Shutting down ExecutorService 'applicationTaskExecutor'

Process finished with exit code 255

```

此时我们再来看下 db0 只有一个user:

![只有一个key](https://i.loli.net/2020/12/31/r1Zjs6HO3MByR5d.png)

### 1.2 原因分析

首先看下StringRedisTmpate的源码

```
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by Fernflower decompiler)
//

package org.springframework.data.redis.core;

import org.springframework.data.redis.connection.DefaultStringRedisConnection;
import org.springframework.data.redis.connection.RedisConnection;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.serializer.RedisSerializer;

public class StringRedisTemplate extends RedisTemplate<String, String> {
    public StringRedisTemplate() {
        this.setKeySerializer(RedisSerializer.string());
        this.setValueSerializer(RedisSerializer.string());
        this.setHashKeySerializer(RedisSerializer.string());
        this.setHashValueSerializer(RedisSerializer.string());
    }

    public StringRedisTemplate(RedisConnectionFactory connectionFactory) {
        this();
        this.setConnectionFactory(connectionFactory);
        this.afterPropertiesSet();
    }

    protected RedisConnection preProcessConnection(RedisConnection connection, boolean existingConnection) {
        return new DefaultStringRedisConnection(connection);
    }
}

```

可以看到 StringRedisTmplate 的 key value hashkey hashvalue 序列话的方式同为RedisSerializer.string()即 `StringRedisSerializer.UTF_8`

```
    static RedisSerializer<String> string() {
        return StringRedisSerializer.UTF_8;
    }
```

再看下RedisConfig中对RedisTmplate 序列化方式的配置
```
    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
        // 创建RedisTemplate<String, Object>对象
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        // 配置连接工厂
        template.setConnectionFactory(factory);
        // 定义Jackson2JsonRedisSerializer序列化对象
        Jackson2JsonRedisSerializer<Object> jacksonSeial = new Jackson2JsonRedisSerializer<>(Object.class);
        ObjectMapper om = new ObjectMapper();
        // 指定要序列化的域，field,get和set,以及修饰符范围，ANY是都有包括private和public
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        // 指定序列化输入的类型，类必须是非final修饰的，final修饰的类，比如String,Integer等会报异常
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jacksonSeial.setObjectMapper(om);
        StringRedisSerializer stringSerial = new StringRedisSerializer();
        // redis key 序列化方式使用stringSerial
        template.setKeySerializer(stringSerial);
        // redis value 序列化方式使用jackson
        template.setValueSerializer(jacksonSeial);
        // redis hash key 序列化方式使用stringSerial
        template.setHashKeySerializer(stringSerial);
        // redis hash value 序列化方式使用jackson
        template.setHashValueSerializer(jacksonSeial);
        template.afterPropertiesSet();
        return template;
    }
```

我们发现 key的序列化方式和StringRedisTmplate是相同的 再结合rdm中看到db0中的key 可以得出结论
**测试方法**中 第7行redisTemplate put后，因为两个模版key的序列化方式相同， 那么第8行执行stringRedisTemplate 的put操作时因为key相同则进行了覆盖 又因为**二、reidsTemplate和stringRedisTemplate**中说到的**数据不共通,各自存取**导致第10行 执行get操作时报错

### 1.3 解决方案：
1. 只用一种模版`reidsTemplate`或`stringRedisTemplate`
2. 两种模版制定不同的key序列化策略
