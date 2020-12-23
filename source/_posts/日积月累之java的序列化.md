---
title: 日积月累之java的序列化
date: 2020-12-23 11:30:00
## updated: 2018-01-05 00:00:00
tags: [Java,基础知识]
categories: 日积月累
keywords: java
description: java序列化
top_img:
comments: true
cover: https://i.loli.net/2020/12/23/7pDl4MaioGZ6tqP.png
toc:
toc_number:
auto_open:
copyright:
mathjax:
katex:
aplayer:
highlight_shrink:
---
<!-- 1.[java序列化，看这篇就够了](https://www.cnblogs.com/9dragon/p/10901448.html)
2.[序列化和反序列化的详解](https://blog.csdn.net/tree_ifconfig/article/details/82766587)
3.[序列化和反序列化](https://blog.csdn.net/tree_ifconfig/article/details/82766587) -->
## 一、Java的序列化与反序列化

### 含义

> Java序列化是指把Java对象转换为字节序列的过程，而

> Java反序列化是指把字节序列恢复为Java对象的过程；

### 意义

（1）**序列化：**在传递和保存对象时.保证对象的完整性和可传递性。对象转换为有序字节流,以便在网络上传输或者保存在本地文件中。

（2）**反序列化：**根据字节流中保存的对象状态及描述信息，通过反序列化重建对象。

（3）**总结：** 核心作用就是**对象状态的保存和重建**。

### 使用场景

（1）所有可在网络上传输的对象都必须是可序列化的，比如RMI（remote method invoke,即远程方法调用）

（2）所有需要保存到磁盘的java对象都必须是可序列化的

### 优点

 ①将对象转为字节流存储到硬盘上，当JVM停机的话，字节流还会在硬盘上默默等待，等待下一次JVM的启动，把序列化的对象，通过反序列化为原来的对象，并且序列化的二进制序列能够减少存储空间（永久性保存对象）。

 ②序列化成字节流形式的对象可以进行网络传输(二进制形式)，方便了网络传输。

 ③通过序列化可以在进程间传递对象。

## 二、实现序列化

>  如果需要将某个对象保存到磁盘上或者通过网络传输，那么这个类应该实现Serializable接口或者Externalizable接口之一。

### 1、Serializable

Serializable接口是一个**标记接口**，不用实现任何方法。一旦实现了此接口，该类的对象就是可序列化的。

#### 1.1 普通序列化
1.序列化
```
@Data
public class User implements Serializable {
    private static final long serialVersionUID = -1266182246612973862L;
    private String name;
    private Integer sex;
    private Integer age;
}
```
```
        ObjectOutputStream oos = null;
        try {
            oos = new ObjectOutputStream(new FileOutputStream("user.txt"));
            User user = new User();
            user.setAge(25);
            user.setSex(1);
            user.setName("Lee");
            oos.writeObject(user);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                oos.close();
            } catch (Exception e) {
                e.printStackTrace();
            }

        }
```

2.反序列化
给User类添加一个无参的构造方法
```
        public User() {
            System.out.println("调用构造方法！");
        }
```

```
        ObjectInputStream ois = null;
        try{
            ois = new ObjectInputStream(new FileInputStream("user.txt"));
            User user = (User)ois.readObject();
            System.out.println(user.toString());
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            try {
                ois.close();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
```
console
```
User(name=Lee, sex=1, age=25)
```
**反序列化并不会调用构造方法。反序列的对象是由JVM自己生成的对象，不通过构造方法生成。**

#### 1.2 类的成员是引用类型的序列化
如果一个可序列化的类的成员不是基本类型，也不是String类型或包装类，那这个引用类型也必须是可序列化的；否则，会导致此类不能序列化。

#### 1.3 序列化多次


```
            User user = new User();
            user.setAge(25);
            user.setSex(1);
            user.setName("Lee");
            User user2 = new User();
            user2.setAge(25);
            user2.setSex(1);
            user2.setName("Lee2");
            oos.writeObject(user);
            oos.writeObject(user2);
            oos.writeObject(user);
            oos.writeObject(user);
```

```
            User user = (User) ois.readObject();
            User user2 = (User) ois.readObject();
            User user3 = (User) ois.readObject();
            User user4 = (User) ois.readObject();
            System.out.println(user==user2);
            System.out.println(user2==user3);
            System.out.println(user==user3);
            System.out.println(user3==user4);
```
console
```
false
false
true
true
```

**结论：**
1. **同一对象序列化多次，不会将这个对象序列化多次**
2. **反序列化的顺序与序列化时的顺序一致**


#### 1.4 java序列化算法

1. 所有保存到磁盘的对象都有一个序列化编码号

2. 当程序试图序列化一个对象时，会先检查此对象是否已经序列化过，只有此对象从未（在此虚拟机）被序列化过，才会将此对象序列化为字节序列输出。

3. 如果此对象已经序列化过，则直接输出编号即可。


#### 1.5 java序列化算法存在的问题

```
            User user = new User();
            user.setAge(25);
            user.setSex(1);
            user.setName("Lee");
            oos.writeObject(user);
            user.setName("Lee2");
            oos.writeObject(user);
```

```
            User user = (User) ois.readObject();
            User user2 = (User) ois.readObject();
            System.out.println(user==user2);
            System.out.println(user.getName());
            System.out.println(user2.getName());
```
console
```
true
Lee
Lee
```

**结论：**
如果序列化一个可变对象（对象内的内容可更改）后，更改了对象内容，再次序列化，并不会再次将此对象转换为字节序列，而只是更新了序列化编号。


#### 1.6 可选的自定义序列化