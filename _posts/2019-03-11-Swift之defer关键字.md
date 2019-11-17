---
layout: post
title: Swift之defer关键字
date: 2019-03-11
tags: Swift
---

defer说是**关键字**并不合适,应该说是**defer语句**,应为使用的时候,一般都是如下
```swift
defer {
   ...
}
```
defer语句:用来定义以任何方式(抛错误,return等)离开代码块前必须要执行的代码

defer语句是一个神奇语句:

它的**核心功能**就是:将括号里的代码延迟至当前作用域结束之前执行

需求如下,`divide(num1: 20, num2: 0)`函数可能抛出异常,那么print(2)是不会执行的,如果想要print(2)一定执行,那么需要defer语句,放在可能抛异常的代码前面
```swift
func processFile() throws {
    print(1)
    
//    defer {
//        print(2)
//    }

    // 可能会抛出异常
    try divide(num1: 20, num2: 0)
    // 注意:如果程序终止了,那么defer也不会执行
    print(2)
}
```
### 注意点: 多个defer语句,它的执行顺序

```swift
func processFile() throws {
    print(1)
    defer {
        print(2)
    }
    defer {
        print(3)
    }
    try divide(num1: 20, num2: 0)
}
 
// 打印顺序1 3 2
```
有点像数据结构中的栈,先进后出

总结: defer语句使用场景,一般是在可能抛出异常的函数后,又必须要执行的代码
