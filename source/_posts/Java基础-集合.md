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

<!-- https://www.cnblogs.com/wangyongwen/p/13661981.html -->
<!-- 
https://i.loli.net/2021/01/03/kjwQRdrgLsFO3IE.png -->
<!-- 
https://i.loli.net/2021/01/11/fMPWkOwyKTbUrxj.png -->

# 一、集合

## 1.预备知识
> jdk版本 1.8
### 1.1 概念

> 集合类是Java数据结构的实现。Java的集合类是java.util包中的重要内容，它允许以各种方式将元素分组，并定义了各种使这些元素更容易操作的方法。Java集合类是Java将一些基本的和使用频率极高的基础类进行封装和增强后再以一个类的形式提供。集合类是可以往里面保存多个对象的类，存放的是对象，不同的集合类有不同的功能和特点，适合不同的场合，用以解决一些实际问题

### 1.2 数据结构

[数据结构](https://leeebean.github.io/leebean.github.io/2020/03/11/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%EF%BC%88%E4%B8%80%EF%BC%89%E5%BF%AB%E9%80%9F%E4%BA%86%E8%A7%A3%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84/)

### 1.3 集合框架设计成要满足的几个目标:

+ 该框架必须是高性能的。

+ 基本集合（动态数组，链表，树，哈希表）的实现也必须是高效的。

+ 该框架允许不同类型的集合，以类似的方式工作，具有高度的互操作性。

+ 对一个集合的扩展和适应必须是简单的。

`总结`:高性能、高效、高可用、可扩展

### 1.4 集合框架体系

+ **接口**，集合的抽象数据类型 （Collection、List、Set、Map）

+ **实现**，集合接口的具体实现，从本质上讲，它们是可重复使用的数据结构，（ArrayList、LinkedList、HashSet、HashMap。）

+ **算法**，是实现集合接口的对象里的方法执行的一些有用的计算，搜索和排序。这些算法被称为多态，那是因为相同的方法可以在相似的接口上有着不同的实现。

![集合框架体系](https://www.runoob.com/wp-content/uploads/2014/01/java-coll-2020-11-16.png)

### 1.5 Java集合框架

![image.png](https://i.loli.net/2020/12/07/dUZ2E6LIGVfa5rX.png)

+ 1.集合框架提供两个遍历接口：Iterator和ListIterator，后者是前者的优化版，支持在集合任意一个位置进行前后双向遍历;
+ 2.整个集合框架分为两个类型：Collection和Map，前者是一个容器，存储一系列的对象；后者是键值对<key, value>，存储一系列的键值对;
+ 3.在现有的集合框架体系下，衍生出四种具体集合类型：Map、Set、List、Queue;
+ 4.Map存储<key,value>键值对，查找元素时通过key查找value;
+ 5.Set内部存储一系列不可重复的对象，且是一个无序集合，对象排列顺序不一;
+ 6.List内部存储一系列可重复的对象，是一个有序集合，对象按插入顺序排列;
+ 7.Queue是一个队列容器，其特性与List相同，但只能从队头和队尾操作元素;
+ 8.JDK 为集合的各种操作提供了两个工具类Collections和Arrays;
+ 9.四种具体类型的集合，内部会再衍生许多不同特性的集合子类，根据不通的应用场景选择使用的集合;

## 2.Collection和Collections
+ Collection 是一个集合接口，提供了对集合对象进行基本操作的通用接口方法。

+ Collections 是一个包装类，内含各种集合操作的静态多态方法，不能实例化（构造方法私有），类似于一个工具类，服务于java Collection框架

## 3.集合和数组

-|集合|数组
:---:|:---|:---
**空间**|可扩容|相对固定
**类型**|只能是引用类型|可以是基本类型也可以是引用类型
**内容**|可以存储不同类型|只能存储同一类型


## 4.Collection有哪些常见方法

方法|描述
:---|:---
int size();|返回集合中元素个数
boolean isEmpty();|判断集合是否为空
boolean contains(Object o);|判断是否包含指定元素（o）
Object[] toArray();|返回一个包含所有元素的Object 数组
boolean add(E e);|添加元素
boolean remove(Object o);|删除元素
boolean containsAll(Collection<?> c);|是否包含指定集合
boolean addAll(Collection<? extends E> c);|将c类集合添加到另一个类集
boolean removeAll(Collection<?> c);|删除c集合内所有元素
void clear();|清空集合
Iterator<E> iterator();|迭代器



## 5.迭代器
### 5.1.Iterator

```
    public interface Iterator<E> {
        boolean hasNext();
        E next();
        void remove();
    }
```

api|描述
:---|:---
boolean hasNext();|判断集合中是否还有下一个对象。
E next();|返回集合中的下一个对象，并将访问指针移动一位。
void remove();|删除集合中调用next()方法返回的对象。

```
    List<Integer> list = new ArrayList<>();
    list.add(10);
    list.add(20);
    list.add(30);
    Iterator iter = list.iterator();
    while (iter.hasNext()) {
        Integer next = iter.next();
        System.out.println(next);
        if (next == 20) {
        iter.remove(); 
        }
    }
```

### 5.2 Iterable
```
    public interface Iterable<T> {
        Iterator<T> iterator();
        default void forEach(Consumer<? super T> action) {
            Objects.requireNonNull(action);
            for (T t : this) {
                action.accept(t);
            }
        }
        default Spliterator<T> spliterator() {
        return Spliterators.spliteratorUnknownSize(iterator(), 0);
        }
    }
```

Iterable接口里面提供了一个Iterator()方法返回迭代器，因此实现了Iterable接口的集合依旧可以使用迭代器遍历和操作集合中的对象。在 JDK 1.8中，Iterable接口新增了一个方法forEach()，它允许使用增强 for 循环遍历对象。

为什么要设计两个接口Iterable和Iterator？Iterator接口的保留可以让子类去实现自己的迭代器，而Iterable接口更加关注于for-each的增强语法。

### 5.3ListIterator

ListIterator继承 Iterator 接口，仅存在于 List 集合之中，通过调用方法可以返回起始下标为 index的迭代器。ListIterator 中有几个重要方法，大多数方法与 Iterator 中定义的含义相同，此外根据返回的迭代器，且可以实现双向遍历。

```
    public interface ListIterator<E> extends Iterator<E> {
        boolean hasNext();
        E next();
        boolean hasPrevious();
        E previous();
        int nextIndex();
        int previousIndex();
        void remove();
        // 替换当前下标的元素,即访问过的最后一个元素
        void set(E e);
        void add(E e);
    }
```




# 二 Collection和Map

![image.png](https://i.loli.net/2021/01/11/yROa2joEXIxdCis.png)
Map接口和Collection接口是Java的集合框架中的两个重要门派，Collection存储的是集合元素本身，Map存储的是<key,value>键值对，但是部分Collection子类使用了Map来实现的。例如：HashSet底层使用了HashMap，TreeSet底层使用了TreeMap，LinkedHashSet底层使用了LinkedHashMap


<table>
    <tr>
        <th> 接口</th>
        <th> 接口</th>
        <th> 抽象类</th>
        <th>描述</th>
    </tr>
    <tr>
        <td rowspan ="3">Collection</td>
        <td>Set</td>
        <td>-</td>
        <td>不允许存储重复元素的无序集合</td>
    </tr>
    <tr>
        <td>List</td>
        <td>-</td>
        <td>可存储重复元素的有序集合</td>
    </tr>
    <tr>
        <td>Queue</td>
        <td>-</td>
        <td>可存储重复元素的队列</td>
    </tr>
    <tr>
        <td rowspan ="2">Map</td>
        <td>AbstracMap</td>
        <td>-</td>
        <td>它为子类提供好一些通用的API实现，所有的具体Map如HashMap都会继承它</td>
    </tr>
    <tr>
        <td>-</td>
        <td>SortedMap</td>
        <td>该类映射可以对<key, value>按照自己的规则进行排序</td>
    </tr>
</table>

# 三、Map

> **Map** 体系下主要分为 **AbstractMap** 和 **SortedMap**两类集合
  **AbstractMap**是对Map接口的扩展，定义了普通Map集合具有的通用方法，可避免子类重复编写大量相同代码，子类继承 AbstractMap 后可重写它的方法，并实现额外逻辑，对外可提供更多的功能。
  **SortedMap**接口定义了Map具有排序行为，当子类实现它时，必须重写所有方法，对外提供排序功能。

## 3.1 HashMap

![HashMap](https://i.loli.net/2021/01/11/fMPWkOwyKTbUrxj.png)

**HashMap** 底层是 `数组` + `链表` + `红黑树` 实现的 非线程安全的集合

HashMap 是一个最通用的利用哈希表存储元素的集合，有元素加入HashMap时，会将key的哈希值转换为数组的索引下标确定存放位置，在查找元素时，根据key的哈希地址转换成数组的索引下标确定查找位置。

加入元素发生哈希冲突时，HashMap会将相同地址的元素连成一条链表，若链表长度大于`8`，且数组长度大于`64`会转换成`红黑树`数据结构。

**总结:**

1. 集合中最常用的Map集合类型，底层由数组 + 链表 + 红黑树组成；
2. HashMap线程不安全
3. 插入元素时，通过计算key的hash值，通过哈希映射函数换为数组下标；查找元素时，通过哈希映射函数得到数组下标定位到元素位置

[**HashMap详解**]()

## 3.2 LinkedHashMap

LinkedHashMap继承了HashMap

## 3.3 TreeMap

## 3.4 WeakHashMap

## 3.5 HashTable
 
# 四、Collection

# 五、集合算法的使用
