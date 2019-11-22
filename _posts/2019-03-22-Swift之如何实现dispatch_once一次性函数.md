---
layout: post
title: Swift之如何实现dispatch_once一次性函数
date: 2019-03-22
tags: Swift
---


1.示例
```swift
// 写在控制器里面
// static 静态的类属性,在程序运行中只初始化一次,在内存只有一份,本质就是全局变量
static var height: Int = getHeight()
static func getHeight() -> Int {
    print("getHeight")
    return 0
}

// 多次调用只打印一次,就算控制器销毁,在此进来控制器,也不会打印了
let _ = Self.height
let _ = Self.height
let _ = Self.height
```

2.示例
```swift
// 写在控制器里面
// 使用闭包声明存储属性
static var age: Int = {
    print("这个是打印")
    return 20
}()

// 多次调用只打印一次,就算控制器销毁,在此进来控制器,也不会打印了
let _ = Self.age
let _ = Self.age
let _ = Self.age
```

3.示例
```swift
// 写在控制器外面,全局变量
var onceTask: Void = {
    print("这个是打印")
}()

// 多次调用只打印一次,就算控制器销毁,在此进来控制器,也不会打印了
let _ = onceTask
let _ = onceTask
let _ = onceTask
```
4.示例
```
// 就算这个控制器销毁,再次进来控制器,这个headView属性仍然是原来的那一份,打印地址可以对比
lazy var headView: UIView = {
    let head = UIView.init()
    head.frame = CGRect.init(x: 0, y: 0, width: 100, height: 100)
    return head
}()
```
一旦使用lazy var closure这种闭包的存储属性,相当于是全局变量,在程序运行过程中它的地址就不会变了,所以,不建议使用这种方法声明属性,一旦生成变无法销毁,除非程序退出.

实际上,也是执行了dispatch_once函数,如图
![demo]({{ "/assets/img/dispatch_once.png" | absolute_url }})
