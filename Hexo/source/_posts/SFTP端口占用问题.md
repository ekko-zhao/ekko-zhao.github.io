---
title: XFTP进程&端口问题
date: 2018-06-21 23:43:40
tags: "主机"
---

在使用XShell和XFtp同时连接一台云主机的时候，发现一个奇怪的现象：
 
使用命令：
``` shell
ps -ef | grep ftp*
```
结果如下:
``` shell
[root@izuf63rdnsbhtikoz6onvyz Hexo]# ps -ef |grep ftp*
root         3     2  0 May31 ?        00:00:07 [ksoftirqd/0]
root     12148 12144  0 23:12 ?        00:00:00 /usr/libexec/openssh/sftp-server
root     12460 12144  0 23:12 ?        00:00:00 /usr/libexec/openssh/sftp-server
root     14237 14234  0 23:27 ?        00:00:00 /usr/libexec/openssh/sftp-server
root     15480 10986  0 23:43 pts/1    00:00:00 grep --color=auto ftp*
```
发现与ftp相关的进程号为12148，12460和14237，于是继续使用命令查查端口：
``` shell
[root@izuf63rdnsbhtikoz6onvyz Hexo]# netstat -anp | grep 12148
[root@izuf63rdnsbhtikoz6onvyz Hexo]# netstat -anp | grep 12460
[root@izuf63rdnsbhtikoz6onvyz Hexo]# netstat -anp | grep 14237
[root@izuf63rdnsbhtikoz6onvyz Hexo]# netstat -anp | grep 12144
tcp        0      0 172.19.209.200:22       180.102.112.242:23968   ESTABLISHED 12144/sshd: root@no 
unix  2      [ ]         DGRAM                    4609066  12144/sshd: root@no  
[root@izuf63rdnsbhtikoz6onvyz Hexo]# netstat -anp | grep 14234
tcp        0      0 172.19.209.200:22       180.102.112.242:25232   ESTABLISHED 14234/sshd: root@no 
unix  2      [ ]         DGRAM                    4616500  14234/sshd: root@no
```
很奇怪，PID并查不到进程，PPID却是可以查到进程。一直知道ftp是通过21端口传输，还没注意过sftp的端口.根据上面的输出，应该是用的22口传输，与ssh同一个端口！

于是百度一番，如下：

**FTP进行文件传输需要通过端口进行。一般所需端口为：**

1. 控制链路—TCP端口21。控制器端。用于发送指令给服务器以及等待服务器响应。

2. 数据链路---TCP端口20。数据传输端口。用来建立数据传输通道的。主要用来从客户向服务器发送一个文件、从服务器向客户发送一个文件、从服务器向客户发送文件或目录列表。

**SFTP是SSH的一部分，是一种传输档案至Blogger伺服器的安全方式。**

它本身没有单独的守护进程，必须使用sshd守护进程来完成相应的连接操作，所以从某种意义上来说，SFTP并不像一个服务器程序，而更像是一个客户端程序。所以说，他占用的端口号也是使用的22咯。

怪不得之前想用FTP连这台主机一直连不上。。。囧