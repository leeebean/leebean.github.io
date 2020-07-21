---
title: Java基础进阶-数据类型
date: 2020-07-15 11:36:00
## updated: 2018-01-05 00:00:00
tags: [Java,基础知识,计算机基础]
categories: 编程语言
keywords: Java
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


### 3.2 存储单位

单位换算：`1024` ( $2^10$ )

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
Java有八种基本数据类型

### 2.1布尔类型-boolean

- 问题1:boolean占几个字节？

> byte: The byte data type is an 8-bit signed two's complement integer. It has a minimum value of -128 and a maximum value of 127 (inclusive). The byte data type can be useful for saving memory in large arrays, where the memory savings actually matters. They can also be used in place of int where their limits help to clarify your code; the fact that a variable's range is limited can serve as a form of documentation.


### 2.2初始值

data type|default value|maximum and minimum
:---:|:---:|:---:
char|'\u0000'|
byte|0|
short|0|
int|0|
long|0L|
float|0.0f|
doubble|0.0d|
boolean|false|


## 3.引用数据类型

## 4.空类型
