---
layout: post
title: Swift之关联对象Associated Object
date: 2019-03-19
tags: Swift
---


## 关联对象Associated Object
在Swift中,可以像Objective-C一样,使用关联对象

### 为什么扩展不能添加存储属性?

因为,**属性涉及到类/结构体的内存结构,所以不能添加属性**,但是可以添加计算属性,因为**计算属性的本质就是方法**

### 如何给扩展添加计算属性?
类似Objective-C中给分类添加属性,手动实现getter/setter方法
```swift
class Student {}
var abc: Int = 22
extension Student {
    var age: Int {
        get {
            // object: Any 关联的是哪一个对象,一般给谁扩展就传哪个对象,因此是self
            // key: UnsafeRawPointer 根据这个地址(当做字典中的key),去关联的对象self中查找value
            objc_getAssociatedObject(self, &abc) as! Int
        }
        set {
            objc_setAssociatedObject(self, &abc, newValue, .OBJC_ASSOCIATION_ASSIGN)
        }
    }
}
```
var abc = 10
### 为什么全局变量可以作为key?
因为是全局变量,因此在内存中地址是唯一的,而且整个程序运行中是不变的,因此&abc可以作为key

### 两个计算属性可以共用一个key吗?
```swift
class Student {}
var abc: Int = 22
extension Student {
    var age: Int {
        get {
            objc_getAssociatedObject(self, &abc) as! Int
        }
        set {
            objc_setAssociatedObject(self, &abc, newValue, .OBJC_ASSOCIATION_ASSIGN)
        }
    }
    
    var weight: Int {
        get {
            objc_getAssociatedObject(self, &abc) as! Int
        }
        set {
            objc_setAssociatedObject(self, &abc, newValue, .OBJC_ASSOCIATION_ASSIGN)
        }
    }
}
```
显而易见,不可以,因为通过&abc这个地址拿到的不能确定是哪个属性


```swift
class Student {}
extension Student {
    
    // 本质就是全局变量:1.内存中地址是唯一的 2.程序运行中是不可变的
    // 我们使用的是变量的地址
    // Bool类型就占用1个字节,能省则省,不要用Int(8个字节),String
    private static var AGE_KEY: Bool = false

    // 也是占用1个字节
    private static var WEIGHT_KEY: Void?
    
    var age: Int {
        get {
            objc_getAssociatedObject(self, &Self.AEG_KEY) as! Int
        }
        set { // Self.AEG_KEY == Student.AEG_KEY
            objc_setAssociatedObject(self, &Student.AEG_KEY, newValue, .OBJC_ASSOCIATION_ASSIGN)
        }
    }
}
```
