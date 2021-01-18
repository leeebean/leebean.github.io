---
title: Java基础-关键字
date: 2020-12-30 14:59:00
tags: [Java,基础知识]
categories: 编程语言
keywords: Java
description: Java的各种关键字
top_img:
comments: true
cover: https://i.loli.net/2020/12/30/7QiuBTIAxh8mdYO.png
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

##  transient

`ArrayList`类和`Vector`类都是使用数组实现的，但是在定义数组`elementData`这个属性时稍有不同，那就是`ArrayList`使用`transient`关键字

```
    private transient Object[] elementData;

    protected Object[] elementData;
```
> Java语言的关键字，变量修饰符，如果用transient声明一个实例变量，当对象存储时，它的值不需要维持。这里的对象存储是指，Java的serialization提供的一种持久化对象实例的机制。当一个对象被序列化的时候，transient型变量的值不包括在序列化的表示中，然而非transient型的变量是被包括进去的。使用情况是：当持久化对象时，可能有一个特殊的对象数据成员，我们不想用serialization机制来保存它。为了在一个特定对象的一个域上关闭serialization，可以在这个域前加上关键字transient。

[日积月累-Java的序列化](https://leeebean.github.io/leebean.github.io/2020/12/23/%E6%97%A5%E7%A7%AF%E6%9C%88%E7%B4%AF-Java%E7%9A%84%E5%BA%8F%E5%88%97%E5%8C%96/)

简单点说，就是被transient修饰的成员变量，在序列化的时候其值会被忽略，在被反序列化后， transient 变量的值被设为初始值， 如 int 型的是 0，对象型的是 null。


## instanceof

1. 二元操作符
2. 保留关键字
3. 判断是否是xxx的实例，返回boolean

        boolean flag = o instanceof List

## final

- 可以使用`final`定义 变量、类、方法

**变量**

- 一旦final变量被定义之后（常量），是无法进行修改的。

**方法**

- 任何方法声明为final，则不能被覆盖(重载)。

**类**

- 任何一个类声明为final，则不能被继承。

## static

[关于Java的静态：静态类、静态方法、静态变量、静态块等](https://zhuanlan.zhihu.com/p/26819685)

**静态变量**

1. 属于类，而不是属于实例
2. 不具有线程安全性，静态变量与所有的对象实例共享
3. 如果静态变量未被私有化，可以用“类名.变量名”的方式来使用。

**静态方法**

1. 属于类，而不是属于实例
2. 静态方法只能使用静态变量，只能调用静态方法

**静态代码块**

1. 静态块常用于初始化类的静态变量
2. 还用于在类装载时候创建静态资源
3. 不允许在静态代码块中使用非静态变量
4. 静态块只在类装载入内存时，执行一次

**静态类**

1. Java可以嵌套使用静态类，但是静态类不能用于嵌套的顶层。
2. 静态嵌套类的使用与其他顶层类一样，嵌套只是为了便于项目打包。


## const

const是Java预留关键字，用于后期扩展用，用法跟final相似，不常用


## volatile

1. 变量修饰符
2. 通常被比喻成"轻量级的`synchronized`

双重锁校验的形式实现的单例
```
    public class Singleton {
        private volatile static Singleton singleton;
        private Singleton (){
        }
        public static Singleton getSingleton() {
            if (singleton == null) {
                synchronized (Singleton.class) {
                    if (singleton == null) {
                        singleton = new Singleton();
                    }
                }
            }
            return singleton;
        }
    }
```

## synchronized

```
public class SynchronizedDemo {
     //同步方法
    public synchronized void doSth(){
        System.out.println("Hello World");
    }

    //同步代码块
    public void doSth1(){
        synchronized (SynchronizedDemo.class){
            System.out.println("Hello World");
        }
    }
}
```

`volatile`和`synchronized`部分的内容待多线程模块中展开


