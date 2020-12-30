---
title: 日积月累-Java的序列化
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

> Java序列化是指把Java对象转换为字节序列的过程

> Java反序列化是指把字节序列恢复为Java对象的过程

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
##### 1.1.1 序列化
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

##### 1.1.2 反序列化
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
**如果一个可序列化的类的成员不是基本类型，也不是String类型或包装类，那这个引用类型也必须是可序列化的；否则，会导致此类不能序列化。**

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

##### 1.6.1 使用transient关键字选择不需要序列化的字段
User 的成员变量
```
    private transient String name;
    private Integer sex;
    private Integer age;
```

序列化
```
    oos = new ObjectOutputStream(new FileOutputStream("user.txt"));
    User user = new User();
    user.setAge(25);
    user.setSex(1);
    user.setName("Lee");
    oos.writeObject(user);
```

反序列化
```
    ois = new ObjectInputStream(new FileInputStream("user.txt"));
    User user = (User) ois.readObject();
    System.out.println(user);
```
console

    User(name=null, sex=1, age=25)

**结论：** 使用transient修饰的属性，java序列化时，会忽略掉此字段，反序列化出的对象，被transient修饰的属性是默认值

##### 1.6.2 可选的自定义序列化

```
    private void writeObject(ObjectOutputStream ous) throws IOException {
        //将名字反转写入二进制流
        ous.writeObject(new StringBuffer(this.name).reverse());
        ous.writeInt(age);
    }

    private void readObject(ObjectInputStream ois) throws IOException,ClassNotFoundException{
        //将读出的字符串反转恢复回来
        this.name = ((StringBuffer)ois.readObject()).reverse().toString();
        this.age = ois.readInt();
    }
```

##### 1.6.3 更彻底的自定义序列化


### 2、Externalizable 强制自定义序列化

```
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
```
```
    try {
        User user = new User();
        user.setAge(25);
        user.setSex(1);
        user.setName("Lee");
        ois = new ObjectInputStream(new FileInputStream("user.txt"));
        oos = new ObjectOutputStream(new FileOutputStream("user.txt"));
        oos.writeObject(user);
        User user1 = (User) ois.readObject();
        System.out.println(user1);
    }catch (Exception e) {
        e.printStackTrace();
    } finally {
        try {
            ois.close();
            oos.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
```

console

    调用构造方法！
    eeL
    调用构造方法
    Lee
    User(name=Lee, sex=null, age=25)

**结论：** Externalizable接口不同于Serializable接口，实现此接口必须实现接口中的两个方法实现自定义序列化，这是强制性的；特别之处是必须提供pulic的无参构造器，因为在反序列化的时候需要反射创建对象。

### 3、对比


实现Serializable接口|实现Externalizable接口
:---|:---
系统自动存储必要的信息|程序员决定存储哪些信息
Java内建支持，易于实现，只需要实现该接口即可，无需任何代码支持|必须实现接口内的两个方法
性能略差|性能略好


## 三、serialVersionUID

    private static final long serialVersionUID = -1266182246612973862L;

> 序列化版本号可自由指定，如果不指定，JVM会根据类信息自己计算一个版本号，这样随着class的升级，就无法正确反序列化；不指定版本号另一个明显隐患是，不利于jvm间的移植，可能class文件没有更改，但不同jvm可能计算的规则不一样，这样也会导致无法反序列化。

> 如果反序列化使用的class的版本号与序列化时使用的不一致，反序列化会报InvalidClassException异常。

**不需要修改serialVersionUID：**

1. 只是修改了方法
2. 只修改了静态变量，瞬态变量（transient修饰的变量）



**需要修改serialVersionUID：**

1. 修改了非瞬态变量，新类中实例变量的类型与序列化时类的类型不一致
2. 只新增了实例变量，则反序列化回来新增的是默认值；
3. 减少了实例变量，反序列化时会忽略掉减少的实例变量。


## 四、总结

1. 所有需要网络传输的对象都需要实现序列化

2. 对象的类名、实例变量会被序列化，方法、类变量（static）、transient实例变量不会被序列化
[实例变量和类变量](https://blog.csdn.net/caigen0001/article/details/89676253)

3. transient修饰的实例变量不会被序列化

4. 序列化对象的引用类型成员变量也必须是可序列化的，否则该对象序列化时会报错

5. 反序列化时必须有序列化对象的class文件

6. 当通过文件、网络来读取序列化后的对象时，必须按照实际写入的顺序读取。

7. 单例类序列化，需要重写readResolve()方法；否则会破坏单例原则。
[Java 写入 readResolve方法解决 单例类序列化后破坏唯一实例规则的问题](https://blog.csdn.net/qcl108/article/details/102258871)

8. 同一对象序列化多次，只有第一次序列化为二进制流，以后都只是保存序列化编号，不会重复序列化。

9. 建议所有可序列化的类加上serialVersionUID 版本号，方便项目升级。


