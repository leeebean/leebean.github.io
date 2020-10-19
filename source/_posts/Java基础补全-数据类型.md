---
title: Java基础补全-数据类型
date: 2020-07-02 14:59:00
## updated: 2018-01-05 00:00:00
tags: [Java,基础知识,计算机基础]
categories: 编程语言
keywords: Java
description: 重温基础，查缺补漏
top_img: https://i.loli.net/2020/08/22/fvwUOzFkW2ujgEM.jpg
comments: true
cover: https://i.loli.net/2020/08/22/fvwUOzFkW2ujgEM.jpg
toc:  
toc_number:
auto_open:
copyright:
mathjax:
katex:
aplayer:
highlight_shrink:
top: true
---

# 一、计算机基础知识

## 1.0和1
> 世界上有10种人：一种是懂得二进制的，另一种是不懂二进制的

0和1代表事物的两种状态

二进制|1|0
:---:|:---:|:---:
阴阳|阳|阴
电灯|通电|断电
开关|开|关
命题|真|假

## 2.二进制
> 二进制（binary）在数学和数字电路中指以2为基数的记数系统，以2为基数代表系统是二进位制的。这一系统中，通常用两个不同的符号0（代表零）和1（代表一）来表示 [1]  。数字电子电路中，逻辑门的实现直接应用了二进制，因此现代的计算机和依赖计算机的设备里都用到二进制。每个数字称为一个比特（Bit，Binary digit的缩写）


二进制的相关知识：
+ [二进制与八卦](https://www.jianshu.com/p/df4186956b58)
+ [如何读懂计算机二进制](https://www.cnblogs.com/mike-mei/p/12157460.html)

## 3.计算机存储单位
> 存储单位是一种计量单位。指在某一领域以一个特定量，或标准做为一个记录（计数）点。再以此点的某个倍数再去定义另一个点，而这个点的代名词就是计数单位或存储单位。

### 3.1 基本存储单元

+ **位（bit）**：二进制数中的一个数位，可以是0或者1，是计算机中数据的最小单位。
+ **字节（Byte，B）**：计算机中数据的基本单位，每8位组成一个字节。各种信息在计算机中存储、处理至少需要一个字节。例如，一个ASCII码用一个字节表示，一个汉字用两个字节表示。
+ **字（Word）**：两个字节称为一个字。汉字的存储单位都是一个字。
+ **字长**：即机器字长，是自然的存储单位，计算机是多少位的，一个字就又多少位。

![字长](https://i.loli.net/2020/07/21/YdacjLP4uSl8eIC.jpg)

一个字节 = 8位（bit）范围：-128(-2<sup>7</sup>) ~ 127(-2<sup>7</sup>-1) 

### 3.2 存储单位

单位换算：-2<sup>10</sup> 

KB = 1024B 

MB = 1024KB

GB = 1024MB

TB = 1024GB

PB = 1024TB

EB = 1024PB

ZB = 1024EB

TB = 1024ZB

BB = 1024TB

ND = 1024BB

DB = 1024ND


# 二、Java数据类型


## 1.数据类型分类

[官网文档](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/datatypes.html)
![Java数据类型](https://i.loli.net/2020/07/21/VCEQBzf7ON6AIZm.png)
## 2.基础数据类型
<table>
	<tr>
	    <th colspan ="3">基本数据类型</th>
        <th>存储</th>
	    <th>范围</th>  
	</tr>
    <tr>
        <td rowspan="6">数值型</td>
	    <td rowspan="4">整型</td>
	    <td>byte</td>
        <td>1个字节(8bit)</td>
        <td>-2<sup>7</sup> ~ -2<sup>7</sup>-1</td>   
	</tr>
    <tr>
	    <td>short</td>
        <td>2个字节(16bit)</td>
        <td>-2<sup>15</sup> ~ -2<sup>15</sup>-1</td>   
	</tr>
    <tr>
	    <td>int</td>
        <td>4个字节(32bit)</td>
        <td>-2<sup>31</sup> ~ -2<sup>31</sup>-1</td>   
	</tr>
    <tr>
	    <td>long</td>
        <td>8个字节(64bit)</td>
        <td>-2<sup>63</sup> ~ -2<sup>63</sup>-1</td>   
	</tr>
    <tr>
	    <td rowspan="2">浮点型</td>
	    <td>float</td>
        <td>4个字节(32bit)</td>
        <td>-</td>   
	</tr>
    <tr>
	    <td>doubble</td>
        <td>8个字节(64bit)</td>
        <td>-</td>  
	</tr>
    <tr>
	    <td colspan ="2">布尔型</td>
	    <td>boolean</td>
        <td>1个字节</td>
        <td>-</td>    
	</tr>
    <tr>
	    <td colspan ="2">字符型</td>
	    <td>char</td>
        <td>2个字节（16bit）</td>
        <td>-</td>  
	</tr>
</table>

### 2.1 为什么要是用基本数据类型？
**节省资源**、**高效**
基本数据类型的数据直接存储在栈内存中，相较于引用类型，不用在堆中`new`一个对象
### 2.2 溢出
```
int a = Integer.MAX_VALUE;
int b = Integer.MIN_VALUE;
int result = a + b;
```
得出结果result为`-2`
[Java中的基本类型转换，数据溢出原理](https://www.cnblogs.com/baby-lily/p/10658051.html)

## 3.引用数据类型
+ **类**
+ **接口**
+ **数据**

与基本数据类型有何不同

数据类型|存储位置|传递方式
:---:|:---:|:---:
基本数据类型|栈内存|数值传递
引用数据类型|堆内存|引用传递

## 4.空类型
1. null是Java关键字，大小写敏感
2. null是任何引用类型的默认值
3. null既不是对象也不是一种类型，它仅是一种特殊的值，你可以将其赋予任何引用类型，你也可以将null转化成任何类型（空引用）
4. null可以赋值给引用变量，不能将null赋值给基本类型变量
5. 任何含有null值的包装类在java拆箱生成基本数据类型时候都会抛出一个空指针异常
6. 如果使用了带有null值的引用类型的变量，instanceof操作会返回false

> 内容摘自[java中的null类型---有关null的9件事](https://blog.csdn.net/qq_25077777/article/details/80174763)

# doubt
- d1:boolean占几个字节？

    引用官网的说明
> byte: The byte data type is an 8-bit signed two's complement integer. It has a minimum value of -128 and a maximum value of 127 (inclusive). The byte data type can be useful for saving memory in large arrays, where the memory savings actually matters. They can also be used in place of int where their limits help to clarify your code; the fact that a variable's range is limited can serve as a form of documentation.

- d2：char占用几个字节

- d3：Integer 和 int 在不同机器上的字长

- d4：引用类和基本数据类型存储在哪儿

    **基本数据类型**在被创建时，在栈上给其划分一块内存，将数值直接存储在栈上。

    **引用数据类型**在被创建时，首先要在栈上给其引用（句柄）分配一块内存，而对象的具体信息都存储在堆内存上，然后由栈上面的引用指向堆中对象的地址。

- d5：Integer是不是引用类型

    [Integer为什么不是引用类型呢？](https://www.imooc.com/qadetail/342469)

    [为什么Ingeger是引用数据类型,但是不是"引用传递"](https://blog.csdn.net/sinat_36572927/article/details/86250494?utm_medium=distribute.pc_relevant_bbs_down.none-task-blog-baidujs-1.nonecase&depth_1-utm_source=distribute.pc_relevant_bbs_down.none-task-blog-baidujs-1.nonecase)

    [奇怪了,Integer不是对象吗,?为什么不按引用传递的](https://bbs.csdn.net/topics/310239741)

    [深入理解Java中方法的参数传递机制](https://www.cnblogs.com/sum-41/p/10799555.html)

- d6：值传递，引用传递
 
    [JAVA中没有引用传递](https://www.cnblogs.com/mamama/p/4627135.html)

## extra

- Bigdecimal
- Integer 的缓存机制
```
Integer a = 1;
Integer b = 2;
System.err.println(a == b);
System.err.println(a.equals(b));
```


## 常见测试题

```
        int i = 1 ;
        int j = i++ ;
        if((i==(++j))&&((i++)==j))     {
            i += j ;
        }
        System.out.println("i = "+i);

        int a = 0;
        for (int i = 0; i < 99; i++) {
            a = a ++;
        }
        System.out.println(a);


        int b = 0;
        for (int i = 0; i < 99; i++) {
            b = ++ b;
        }
        System.out.println(b);


        Integer a = 0;
        int b = 0;
        for (int i = 0; i < 99; i++) {
            a = a ++;
            b = a ++;
        }
        System.out.println(a);
        System.out.println(b);


        int num = 50 ;
        num = num ++ * 2 ;
        System.out.println(num) ;

        int i = 1 ;
        int j = i++ ;
        if((i==(++j))&&((i++)==j)) {
            i += j ;
        }
        System.out.println("i = "+i);
```
