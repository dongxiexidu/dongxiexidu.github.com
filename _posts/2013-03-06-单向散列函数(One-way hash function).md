---
layout: post
title: 单向散列函数(One-way hash function)
date: 2013-03-06
tags: 加密
---

>单向散列函数，可以根据根据消息内容计算出散列值

### 单向散列函数，又被称为消息摘要函数（message digest function），哈希函数
### 输出的散列值，也被称为消息摘要（message digest）、指纹（fingerprint）


>散列值的长度和消息的长度无关，无论消息是1bit、10M、100G，单向散列函数都会计算出固定长度的散列值

![demo]({{ "/assets/img/oneway.jpg" | absolute_url }})
![demo]({{ "/assets/img/hash.png" | absolute_url }})


单向散列函数的特点

- 1.根据任意长度的消息，计算出固定长度的散列值

- 2.计算速度快，能快速计算出散列值

- 3.消息不同，散列值也不同

- 4.具备单向性

![demo]({{ "/assets/img/singleway.png" | absolute_url }})
![demo]({{ "/assets/img/onlyValue.png" | absolute_url }})

### 常见的几种单向散列函数
**MD4、MD5**
>产生128bit的散列值，MD就是Message Digest的缩写，目前已经不安全

### Mac终端上默认可以使用md5命令

示例,在终端上查看md5指令
```swift
md5 --help
```

输出
```swift
md5: illegal option -- -
usage: md5 [-pqrtx] [-s string] [files ...]
```

使用md5
```swift
md5 -s "123"
```
输出
```swift
MD5 ("123") = 202cb962ac59075b964b07152d234b70
```

在桌面新建1.txt文件,输入123,查看文件的md5
```swift
cd ~/Desktop/
touch 1.txt
open 1.txt
MD5 1.txt
```
输出
```swift
MD5 (1.txt) = 202cb962ac59075b964b07152d234b70
```
可见值相同,那么md5值也相同



>SHA-1
产生160bit的散列值，目前已经不安全

>SHA-2
SHA-256、SHA-384、SHA-512，散列值长度分别是256bit、384bit、512bit

>SHA-3
全新标准,专家正在研究中


## 单向散列函数的应用 

### 1.验证数据是否被篡改

![demo]({{ "/assets/img/dataModify.png" | absolute_url }})

再比如这个远程登录软件,每个软件版本都标注了散列值
https://www.realvnc.com/en/connect/download/vnc/

![demo]({{ "/assets/img/SHA-256.jpg" | absolute_url }})

### 2.口令加密
![demo]({{ "/assets/img/sha-2.jpg" | absolute_url }})

这也就是为啥没有一个平台有找回密码的功能,,只有重置密码或者忘记密码,因为他们平台也不知道密码


