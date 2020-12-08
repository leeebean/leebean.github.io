---
title: Java基础-集合
date: 2020-03-11 14:59:00
## updated: 2018-01-05 00:00:00
tags: [Java,基础知识]
categories: 编程语言
keywords: [Java,集合]
description: 重温基础，查缺补漏
top_img:
comments: true
cover: https://i.loli.net/2020/12/07/dUZ2E6LIGVfa5rX.png
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

# 集合
> 集合类是Java数据结构的实现。Java的集合类是java.util包中的重要内容，它允许以各种方式将元素分组，并定义了各种使这些元素更容易操作的方法。Java集合类是Java将一些基本的和使用频率极高的基础类进行封装和增强后再以一个类的形式提供。集合类是可以往里面保存多个对象的类，存放的是对象，不同的集合类有不同的功能和特点，适合不同的场合，用以解决一些实际问题。

## 课前预备知识

+ [数据结构](https://leeebean.github.io/leebean.github.io/2020/03/11/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%EF%BC%88%E4%B8%80%EF%BC%89%E5%BF%AB%E9%80%9F%E4%BA%86%E8%A7%A3%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/)

集合框架被设计成要满足以下几个目标。

+ 该框架必须是高性能的。基本集合（动态数组，链表，树，哈希表）的实现也必须是高效的。

+ 该框架允许不同类型的集合，以类似的方式工作，具有高度的互操作性。

+ 对一个集合的扩展和适应必须是简单的。

`总结`:高性能、高可用、可扩展

![image.png](https://i.loli.net/2020/12/07/dUZ2E6LIGVfa5rX.png)

## Collection和Collections
+ Collection 是一个集合接口，提供了对集合对象进行基本操作的通用接口方法。

+ Collections 是一个包装类，内含各种集合操作的静态多态方法，不能实例化（构造方法私有），类似于一个工具类，服务于java Collection框架