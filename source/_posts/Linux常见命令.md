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

## rm

`语法`：rm [options] name

+ -i  --interactive 删除既有文件或目录之前先询问用户
+ -f  --force 强制删除文件或目录，不会出现警告
+ -r  -R 或--recursive 递归处理，将指定目录下的所有文件及子目录一并处理，较危险，慎用
+ -rf 强制删除指定目录下的文件或目录和目录下的所有文件

**原理**:
> Linux通过文件link的数量控制文件的删除，只有当一个文件不存在任何link的时候且没有程序调用的时候，文件才会被真正删除。
就rm命令而言，就是减少磁盘引用计数i_link(文件到inode的链接数量)；inode节点指向存储数据的block,删除文件并不是清除inode和block，而是将文件的硬链接为0，引用计数为0 才能删除文件

注：如果有新的数据存储或者系统通过类似fsck命令做磁盘检查的时候，被删除的数据块和目录会被释放，数据无法找回

[可以备份，尽量不要删除，比删除更好的是重命名->只删除文件链接，重启、重新写入后回收](https://www.cnblogs.com/ftl1012/p/9247107.html)

```
➜  linux-command-practice mkdir test
➜  linux-command-practice cd test
➜  test touch a.txt b.txt c.txt
➜  test ll
total 0
-rw-r--r--  1 lee  staff     0B 12  4 16:58 a.txt
-rw-r--r--  1 lee  staff     0B 12  4 16:58 b.txt
-rw-r--r--  1 lee  staff     0B 12  4 16:58 c.txt
➜  test rm a.txt
➜  test cd ..
➜  linux-command-practice rm test
rm: test: is a directory
➜  linux-command-practice cd test
➜  test rm -r *
zsh: sure you want to delete all 2 files in /Users/lee/Desktop/linux-command-practice/test [yn]? y
➜  test ll
➜  test cd ..
➜  linux-command-practice ll
total 0
drwxr-xr-x  2 lee  staff    64B 12  4 16:59 test
-rw-r--r--  1 lee  staff     0B 11 11 11:11 test.log
-rw-r--r--  1 lee  staff     0B 11 11 11:11 test2.log
➜  linux-command-practice rm -r test
➜  linux-command-practice ll
total 0
-rw-r--r--  1 lee  staff     0B 11 11 11:11 test.log
-rw-r--r--  1 lee  staff     0B 11 11 11:11 test2.log
➜  linux-command-practice
```


## mkdir

make directory 创建目录

`语法`：mkdir [-p] dirName

+ -p 确保目录名称存在，不存在的就建一个。

```
➜  linux-command-practice mkdir test
➜  linux-command-practice ll
total 0
drwxr-xr-x  2 lee  staff    64B 12  7 09:33 test
-rw-r--r--  1 lee  staff     0B 11 11 11:11 test.log
-rw-r--r--  1 lee  staff     0B 11 11 11:11 test2.log
➜  linux-command-practice mkdir test/inner
➜  linux-command-practice ll
total 0
drwxr-xr-x  3 lee  staff    96B 12  7 09:33 test
-rw-r--r--  1 lee  staff     0B 11 11 11:11 test.log
-rw-r--r--  1 lee  staff     0B 11 11 11:11 test2.log
➜  linux-command-practice cd test
➜  test ll
total 0
drwxr-xr-x  2 lee  staff    64B 12  7 09:33 inner
➜  test cd ..
➜  linux-command-practice mkdir -p test
➜  linux-command-practice ll
total 0
drwxr-xr-x  3 lee  staff    96B 12  7 09:33 test
-rw-r--r--  1 lee  staff     0B 11 11 11:11 test.log
-rw-r--r--  1 lee  staff     0B 11 11 11:11 test2.log
➜  linux-command-practice mkdir test2/inner
mkdir: test2: No such file or directory
➜  linux-command-practice
```

## cat

concatenate 命令用于连接文件并打印到标准输出设备上

`语法`：cat [-AbeEnstTuv] [--help] [--version] fileName

-n 或 --number：由 1 开始对所有输出的行数编号。
-b 或 --number-nonblank：和 -n 相似，只不过对于空白行不编号。
-s 或 --squeeze-blank：当遇到有连续两行以上的空白行，就代换为一行的空白行。
-v 或 --show-nonprinting：使用 ^ 和 M- 符号，除了 LFD 和 TAB 之外。
-E 或 --show-ends : 在每行结束处显示 $。
-T 或 --show-tabs: 将 TAB 字符显示为 ^I。
-A, --show-all：等价于 -vET。
-e：等价于"-vE"选项；
-t：等价于"-vT"选项；

```
➜  linux-command-practice ll
total 2176
-rw-r--r--  1 lee  staff   1.1M 12  6 15:27 practice.log
drwxr-xr-x  3 lee  staff    96B 12  7 09:33 test
-rw-r--r--  1 lee  staff     0B 11 11 11:11 test.log
-rw-r--r--  1 lee  staff     0B 11 11 11:11 test2.log
➜  linux-command-practice cat practice.log
    (Slf4jLoggerFactory.java:95)][0][] INFO - closeChannel: close the connection to remote address[] result: true
    2020-12-06 15:26:42.482 [wms-inner/Lees-MacBook-Pro.local/127.0.0.1][autocompleteAsn_QuartzSchedulerThread][][org.quartz.core.QuartzSchedulerThread.run(QuartzSchedulerThread.java:291)][0][] DEBUG - batch acquisition of 0 triggers
    2020-12-06 15:26:42.833 [wms-inner/Lees-MacBook-Pro.local/127.0.0.1][sendDingDingMsg_Worker-1][][org.apache.curator.RetryLoop.takeException(RetryLoop.java:193)][0][] DEBUG - Retrying operation
    2020-12-06 15:26:42.834 [wms-inner/Lees-MacBook-Pro.local/127.0.0.1][ReconcileService RUNNING][][org.apache.curator.ConnectionState.checkTimeouts(ConnectionState.java:200)][0][] ERROR - Connection timed out for connection string (10.111.31.238:2560) and timeout (15000) / elapsed (51828)
➜  linux-command-practice cat test.log
  9667	2020-12-06 15:27:00.012 [wms-inner/Lees-MacBook-Pro.local/127.0.0.1][Quartz Shutdown-Hook desktopTemporaryUserExpired][][org.quartz.simpl.SimpleThreadPool.shutdown(SimpleThreadPool.java:328)][0][] DEBUG - Shutting down threadpool...
  9668	2020-12-06 15:27:00.012 [wms-inner/Lees-MacBook-Pro.local/127.0.0.1][Quartz Shutdown-Hook executeGenerateOutstockSchedule][][org.quartz.simpl.SimpleThreadPool.shutdown(SimpleThreadPool.java:328)][0][] DEBUG - Shutting down threadpool...
  9669	2020-12-06 15:27:00.013 [wms-inner/Lees-MacBook-Pro.local/127.0.0.1][Quartz Shutdown-Hook storeContainerNotReturnAlarm][][org.quartz.simpl.SimpleThreadPool.shutdown(SimpleThreadPool.java:328)][0][] DEBUG - Shutting down threadpool...
  9670	2020-12-06 15:27:00.026 [wms-inner/Lees-MacBook-Pro.local/127.0.0.1][Thread-33][][org.apache.dubbo.spring.boot.context.event.AwaitingNonWebApplicationListener.lambda$release$2(AwaitingNonWebApplicationListener.java:162)][0][] INFO -  [Dubbo] Current Spring Boot Application is about to shutdown...
➜  linux-command-practice cat /dev/null > test.log
➜  linux-command-practice cat test.log
➜  linux-command-practice cat -b practice.log test.log >> test2.log
➜  linux-command-practice cat test2.log
...
  9665	2020-12-06 15:27:00.012 [wms-inner/Losters-MacBook-Pro.local/127.0.0.1][Quartz Shutdown-Hook executeGenerateOutstockSchedule][][org.quartz.simpl.SimpleThreadPool.shutdown(SimpleThreadPool.java:328)][0][] DEBUG - Shutting down threadpool...
  9666	2020-12-06 15:27:00.013 [wms-inner/Losters-MacBook-Pro.local/127.0.0.1][Quartz Shutdown-Hook storeContainerNotReturnAlarm][][org.quartz.simpl.SimpleThreadPool.shutdown(SimpleThreadPool.java:328)][0][] DEBUG - Shutting down threadpool...
  9667	2020-12-06 15:27:00.026 [wms-inner/Losters-MacBook-Pro.local/127.0.0.1][Thread-33][][org.apache.dubbo.spring.boot.context.event.AwaitingNonWebApplicationListener.lambda$release$2(AwaitingNonWebApplicationListener.java:162)][0][] INFO -  [Dubbo] Current Spring Boot Application is about to shutdown...
```


