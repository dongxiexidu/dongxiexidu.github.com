---
layout: post
title: iOS之Runtime简介
date: 2015-07-03
tags: Runtime
---
## Runtime简介
- 1.Objective-C是一门动态性比较强的编程语言,跟C/C++等语言有着很大的不同
- 2.Objective-C的动态性是由Runtime API来支撑的
- 3.Runtime API提供的接口基本都是C语言的,源码是由C/C++/汇编语言编写的



## isa简介
### instance的isa指向class
当调用对象方法时，通过instance的isa找到class，最后找到对象方法的实现进行调用

### class的isa指向meta-class
当调用类方法时，通过class的isa找到meta-class，最后找到类方法的实现进行调用

![demo]({{ "/assets/img/isa.jpg" | absolute_url }})

在arm64之前,isa就是一个普通的指针,存储着Class/Meta-Class对象的内存地址

从64bit开始，isa需要进行一次位运算，才能计算出真实地址

![demo]({{ "/assets/img/isa&isa_mask.jpg" | absolute_url }})
![demo]({{ "/assets/img/isa_arm64.png" | absolute_url }})
