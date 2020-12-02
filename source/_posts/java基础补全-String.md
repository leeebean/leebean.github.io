---
title: Java基础补全-String
date: 2020-07-02 14:59:00
## updated: 2018-01-05 00:00:00
tags: [Java,基础知识,计算机基础]
categories: 编程语言
keywords: [Java,String]
description: 重温基础，查缺补漏
top_img:
comments: true
cover:
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
# String
## 字符串的不可变性

```
String s = "abc";
```
![s.png](https://i.loli.net/2020/10/29/bnp2eRoZJsQfq9M.png)
```
String s2 = "abc";
```
![s2.png](https://i.loli.net/2020/10/29/7VcB46NfdEDULl2.png)
```
s2 = "def";
```
![s2.png](https://i.loli.net/2020/10/29/M3yZndPlYecshp8.png)
```
s2 = s2.concat("g");
```
`源码`
```
    public String concat(String str) {
        int otherLen = str.length();
        if (otherLen == 0) {
            return this;
        }
        int len = value.length;
        char buf[] = Arrays.copyOf(value, len + otherLen);
        str.getChars(buf, len);
        return new String(buf, true);
    }
```
从源码中可以看出 return 了一个新的String对象
![s2.png](https://i.loli.net/2020/10/29/sobyAcumYFe4wK9.png)

>一旦一个String对象在内存(堆)中被创建出来，他就无法被修改。特别要注意的是，String类的所有方法都没有改变字符串本身的值，都是返回了一个新的对象。

>如果你需要一个可修改的字符串，应该使用StringBuffer 或者 StringBuilder。否则会有大量时间浪费在垃圾回收上，因为每次试图修改都有新的string对象被创建出来。

###  原理

#### 不变模式
>[《JAVA与模式》之不变模式](https://www.cnblogs.com/java-my-life/archive/2012/05/08/2487757.html)

**1. 概念**
不变模式 , 行为型模式一种 ，系统使用某个对象的过程中 , 需要创建数量较多的同类对象 , 同时大量对象中还普遍存在状态相同的情况 , 此时就需要简化对象状态 , 使相同状态的对象的量减少 , 降低资源的消耗 。
                                                                            --百度百科
**2.特点**
不变模式是线程友好的，一旦一个对象被创建在任何情况下任何线程包括该对象内部都无法修改其内部数据，因此多线程操作下不需要对其做同步控制。

#### 如何实现不可变
> 《effective java》 第四章-类和接口 第十五条：使可变性最小化
实现不可变至少需要五个条件
1. 不要提供任何会修改对象状态的方法
2. 保证类不会被扩展
3. 使得所有的域都是final的
4. 使得所有的域都成为私有
5. 确保对于任何可变组件的互斥访问
举例：
```
TestClass(String str){
    this.str = new String(str);
}
```


### 从源码上看
`jdk版本 1.8`
```
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {
    /** The value is used for character storage. */
    private final char value[];

    /** Cache the hash code for the string */
    private int hash; // Default to 0

    /** use serialVersionUID from JDK 1.0.2 for interoperability */
    private static final long serialVersionUID = -6849794470754667710L;
}
```
JDK1.8 String 只有两个属性: value[]、hash 且都是私有且不可变的，Stirng 提供的所有方法都没有改变原对象，而是返回一个新的String 对象

### String真的不可变吗？
利用反射可以改变value属性
```
    String str = "abc";
    System.out.println("before:" + str);
    System.out.println("before:" + System.identityHashCode(str));
    // 获取String类中的value字段
    Field valueField = String.class.getDeclaredField("value");
    // 改变value属性的访问权限
    valueField.setAccessible(true);
    // 获取str对象上value属性的值
    char[] value = (char[]) valueField.get(str);
    value[2] = 'd';
    System.out.println("after:" + str);
    System.out.println("after:" + System.identityHashCode(str));
```
控制台打印结果：
```
before:abc
before:1639705018
after:abd
after:1639705018
```

### 好处：安全+高效
+ 好处：
    + 安全：
        + 多线程
        + 类加载
    + 高效：
        + 节省空间
+ 缺点：
    + 会创造很多的垃圾，给垃圾收集带来很大的麻烦

### extra
[浅析String不可变性](https://www.cnblogs.com/think-in-java/p/6127804.html)

## substring

### substring()

```
    //JDK8
    public String substring(int beginIndex, int endIndex) {
        if (beginIndex < 0) {
            throw new StringIndexOutOfBoundsException(beginIndex);
        }
        if (endIndex > value.length) {
            throw new StringIndexOutOfBoundsException(endIndex);
        }
        int subLen = endIndex - beginIndex;
        if (subLen < 0) {
            throw new StringIndexOutOfBoundsException(subLen);
        }
        return ((beginIndex == 0) && (endIndex == value.length)) ? this
                : new String(value, beginIndex, subLen);
    }

        /**
     * Allocates a new {@code String} that contains characters from a subarray
     * of the character array argument. The {@code offset} argument is the
     * index of the first character of the subarray and the {@code count}
     * argument specifies the length of the subarray. The contents of the
     * subarray are copied; subsequent modification of the character array does
     * not affect the newly created string.
     *
     * @param  value
     *         Array that is the source of characters
     *
     * @param  offset
     *         The initial offset
     *
     * @param  count
     *         The length
     *
     * @throws  IndexOutOfBoundsException
     *          If the {@code offset} and {@code count} arguments index
     *          characters outside the bounds of the {@code value} array
     */
    public String(char value[], int offset, int count) {
        if (offset < 0) {
            throw new StringIndexOutOfBoundsException(offset);
        }
        if (count <= 0) {
            if (count < 0) {
                throw new StringIndexOutOfBoundsException(count);
            }
            if (offset <= value.length) {
                this.value = "".value;
                return;
            }
        }
        // Note: offset or count might be near -1>>>1.
        if (offset > value.length - count) {
            throw new StringIndexOutOfBoundsException(offset + count);
        }
        this.value = Arrays.copyOfRange(value, offset, offset+count);
    }
```
```
    //JDK 6
    String(int offset, int count, char value[]) {
        this.value = value;
        this.offset = offset;
        this.count = count;
    }

    public String substring(int beginIndex, int endIndex) {
        //check boundary
        return  new String(offset + beginIndex, endIndex - beginIndex, value);
    }
```








## String 源码分析

[享元模式](https://blog.csdn.net/yulungggg/article/details/81039655)