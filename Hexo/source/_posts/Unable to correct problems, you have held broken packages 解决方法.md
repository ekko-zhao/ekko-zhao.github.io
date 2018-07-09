---
title: Unable to correct problems, you have held broken packages 解决方法
date: 2018-06-24 13:43:40
tags: "主机"
---

使用Ubuntu安装软件的时候经常遇到 E: Unable to correct problems, you have held broken packages的错误，意为软件包冲突了。

以我今天安装msql时的错误为例，安装时提示如下：
``` shell
ekko@ubuntu:~$ sudo apt-get install mysql-server 
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Some packages could not be installed. This may mean that you have
requested an impossible situation or if you are using the unstable
distribution that some required packages have not yet been created
or been moved out of Incoming.
The following information may help to resolve the situation:

The following packages have unmet dependencies:
 mysql-server : Depends: mysql-server-5.5 but it is not going to be installed
E: Unable to correct problems, you have held broken packages.
```
解决方法如下：
``` shell
#删除旧的mysql
$ dpkg -l | grep mysql  #找到所有已安装的对应mysql的包
$ sudo dpkg --purge --force-all mysqlxxxx #一一删除上面列出的有关mysql的包
$ sudo apt-get -f install #强制安装所有欠缺的包
#安装新的mysql
$ sudo apt-get install mysql-server
$ sudo apt isntall mysql-client
$ sudo apt install libmysqlclient-dev
```
安装完成了，试试登陆本地mysql:
``` shell
$ mysql -uroot -pyourpass
```
