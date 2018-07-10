---
title: python使用小方法（持续更新）
date: 2018-06-27 13:43:40
tags: "python"
---

在这里记录下学习python过程中的小Tips ~~

***输入数字***

确保输入的为数字方法。
``` python
def int_input(in):
    while 1:
        try:
            a = int(input(in))
            break
        except:
            print('请输入一个数字:')
    return a
```
***encode()与decode()***

编码是一种用一种特定的方式对抽象字符（Unicode）转换为二进制形式（bytes）进行表示，也就是python3中的encode。解码就是对用特定方式表示的二进制数据用特定的方式转化为Unicode，也就是decode。
``` python
>>>import urllib.request
>>>response = urllib.request.urlopen(‘http://www.baidu.com’)
>>>html = response.read()
>>>type(html)
<class 'bytes'>
>>html = response.read().decode('utf-8') #所以想要正常显示就需要使用decode
```

***优雅地中断进程***

使用threading地时候经常遇到Ctrl+C无法中断程序的尴尬情况。对比了百度到的各种方法，感觉下面这种最简略方便了。
先定义一个quit函数：
``` python
def quit(signum, frame):
    print('You choose to stop me.')
    exit()
```
然后把多线程任务加到try语句中，前面加入信号检测：
``` python
 try:
    signal.signal(signal.SIGINT, quit)
    signal.signal(signal.SIGTERM, quit)

    a = threading.Thread(target = printA)
    b = threading.Thread(target = printB)
    a.setDaemon(True)
    a.start()
    b.setDaemon(True)
    b.start()

    while True:
        pass
except Exception, exc:
    print exc
```
***URL格式化***

很简单的转换，记录一下。
``` pyhton
host_name = input('输入：')
host_name = str(host_name)
if 'http://' in host_name or 'https://' in host_name:
	host_name = host_name[host_name.find('://') + 3:]
```
***cookie分割***

把cookie值复制到txt中，分割后存入字典。
``` python
f = open('cookies.txt','r')
cookies = {}
for line in f.read().split(';'):
	name,value = line.strip().split('=',1)
	cookies[name] = value
```
