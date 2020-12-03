---
title: Linux常见命令
date: 2020-03-11 23:59:59
tags: Linux
categories: Linux
keywords: [Linux,命令,操作系统]
description: Linux常见命令大全
top_img:
comments: true
cover:  https://i.loli.net/2020/12/02/UHArQETK6MjnJXO.jpg
toc:
toc_number:
auto_open:
copyright:
mathjax:
katex:
aplayer:
highlight_shrink:
---
> Linux，全称GNU/Linux，是一种免费使用和自由传播的类UNIX操作系统，其内核由林纳斯·本纳第克特·托瓦兹于1991年10月5日首次发布，它主要受到Minix和Unix思想的启发，是一个基于POSIX和Unix的多用户、多任务、支持多线程和多CPU的操作系统。它能运行主要的Unix工具软件、应用程序和网络协议。它支持32位和64位硬件。Linux继承了Unix以网络为核心的设计思想，是一个性能稳定的多用户网络操作系统。Linux有上百种不同的发行版，如基于社区开发的debian、archlinux，和基于商业开发的Red Hat Enterprise Linux、SUSE、oracle linux等。

[https://man.linuxde.net/](https://man.linuxde.net/)

# 1.文件管理
## touch

touch命令不常用，在使用make的时候，用以修改文件时间戳，若无该文件则新建一个文件

`语法`：touch [-acfm][-d<日期时间>][-r<源文件或目录>] [-t<日期时间>][--help][--version][目标文件或目录…]

+ -a 改变档案的读取时间记录。
+ -m 改变档案的修改时间记录。
+ -c 假如目的档案不存在，不会建立新的档案。与 --no-create 的效果一样。
+ -f 不使用，是为了与其他 unix 系统的相容性而保留。
+ -r 使用参考档的时间记录，与 --file 的效果一样。
+ -d 设定时间与日期，可以使用各种不同的格式。
+ -t 设定档案的时间记录，格式与 date 指令相同。
+ --no-create 不会建立新档案。
+ --help 列出指令格式。
+ --version 列出版本讯息。

```
➜  ~ cd  Desktop
➜  Desktop mkdir linux-command-practice
➜  Desktop cd linux-command-practice
➜  linux-command-practice touch test.log
➜  linux-command-practice ll
total 0
-rw-r--r--  1 lee  staff     0B 12  3 16:25 test.log
➜  linux-command-practice touch test.log
➜  linux-command-practice ll
total 0
-rw-r--r--  1 lee  staff     0B 12  3 16:28 test.log
➜  linux-command-practice touch -c test2.log
➜  linux-command-practice ll
total 0
-rw-r--r--  1 lee  staff     0B 12  3 16:28 test.log
➜  linux-command-practice touch test2.log
➜  linux-command-practice ll
total 0
-rw-r--r--  1 lee  staff     0B 12  3 16:28 test.log
-rw-r--r--  1 lee  staff     0B 12  3 16:29 test2.log
➜  linux-command-practice touch -t 202011111111 test2.log
➜  linux-command-practice ll
total 0
-rw-r--r--  1 lee  staff     0B 12  3 16:28 test.log
-rw-r--r--  1 lee  staff     0B 11 11 11:11 test2.log
➜  linux-command-practice touch -r test2.log test.log
➜  linux-command-practice ll
total 0
-rw-r--r--  1 lee  staff     0B 11 11 11:11 test.log
-rw-r--r--  1 lee  staff     0B 11 11 11:11 test2.log
➜  linux-command-practice
```