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

***python3打开UTF-8-BOM文件***

###### 方法1：
以二进制的方法打开文件，读取内容后解码。
```pthon
with open('passwdtop10000.txt','rb') as f:
passwords = f.readlines()
passwords[1] = passwords[1].decode('utf-8-sig')
```
###### 方法2：
open方法加上encoding参数。
```python
with open('passwdtop10000.txt','r',encoding='utf-8-sig') as f:
	passwords = f.readlines()
```
###### 方法3：
搜索文件前三个字符，判断是否为\xef\xbb\xbf。
```python
def removeBom(file):
    '''移除UTF-8文件的BOM字节'''
    BOM = b'\xef\xbb\xbf'
    existBom = lambda s: True if s == BOM else False
    f= open(file, 'rb')
    if existBom(f.read(3)):
        fbody = f.read()
        # f.close()
        with open(file, 'wb') as f:
            f.write(fbody)

if __name__ == '__main__':
    for root, dirs, files in os.walk("d:\\桌面\\国舜\\民生银行-20180716\\0725"):
        for file in files:
            if file.find(".txt") != -1:
                removeBom(os.path.join(root, file))
```
***UnicodeDecodeError***

打开文本经常遇到如下错误错误：
```python
>>>python3 check.py
Traceback (most recent call last):
  File "check.py", line 5, in <module>
    passwords = f.readlines()
UnicodeDecodeError: 'gbk' codec can't decode byte 0xbf in position 2: illegal multibyte sequence
```
解决方法：
```python
with open('passwdtop10000.txt',encoding='UTF-8') as f:  #encoding='UTF-8',解除UnicodeDecodeError,或者以二进制打开，然后解码。
	passwords = f.readlines()
```