---
title: Hello World!
date: 2020-07-01 11:30:00
## updated: 2018-01-05 00:00:00
tags: md
categories: 日积月累
keywords: md
description: 我的第一篇文章
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
## 博客框架
[HEXO](https://hexo.io/)
## 免费的图床服务
[SM.MS](https://sm.ms/)       

> 这是我的第一篇文章 先来熟悉一下MarkDown的基础语法
## MarkDown基础语法
### 标题
```
# 一级标题
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题
###### 六级标题
```

效果如下

![图片](https://i.loli.net/2020/07/14/w8MO1DAZuXBUmyH.png)

### 引用
```
> 停车做爱枫林晚
```
> 停车做爱枫林晚


### 链接和图片
[点击跳转至百度](http://www.baidu.com)
![图片](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1594641409433&di=bafe065c2d2379984e1a971a0e7d4cab&imgtype=0&src=http%3A%2F%2Fn.sinaimg.cn%2Fsinacn%2Fw1920h1080%2F20180130%2Fbabe-fyrcsrv8730487.jpg)

### 列表
```
* JAVA
* DUBBO
* SpringCloud

+ JAVA
+ DUBBO
+ SpringCloud

- JAVA
- DUBBO
- SpringCloud

1. JAVA
2. DUBBO
3. SpringCloud
```
效果如下
* JAVA
* DUBBO
* SpringCloud

+ JAVA
+ DUBBO
+ SpringCloud

- JAVA
- DUBBO
- SpringCloud

1. JAVA
2. DUBBO
3. SpringCloud

### 分割线
```
* * *
***
*****
- - -
-----------
```
* * *
***
*****
- - -
-----------

### 强调
```
*斜体*或_斜体_
**粗体**
***加粗斜体***
~~删除线~~
++下划线++
==背景高亮==

`标红`
```

*斜体*或_斜体_

**粗体**

***加粗斜体***

~~删除线~~

`标红`

### 插入代码

使用 ` ``` ` 

```
	class Solution {
	    public String largestTimeFromDigits(int[] A) {
	        int[] f = new int[10];
	        for(int x : A)f[x]++;
	        for(int h = 23;h >= 0;h--){
	        	for(int m = 59;m >= 0;m--){
	        		int[] g = new int[10];
	        		for(int i = h, j = 0;j < 2;i /= 10,j++)g[i%10]++;
	        		for(int i = m, j = 0;j < 2;i /= 10,j++)g[i%10]++;
	        		if(Arrays.equals(f, g))return String.format("%02d:%02d", h, m);//将h，m以两位的形式按字符串输出
	        	}
	        }
	        return "";
	    }
	}
```

### 表格
```
head|title|title
:---:|:---:|:---:
content|content|content
```
head|title|title
:---:|:---:|:---:
content|content|content


### 特殊符号

```
\   反斜线
`   反引号
*   星号
_   底线
{}  花括号
[]  方括号
()  括弧
#   井字号
+   加号
-   减号
.   英文句点
!   惊叹号
```


