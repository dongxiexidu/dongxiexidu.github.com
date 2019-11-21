---
layout: post
title: Swift之KVC和KVO
date: 2019-03-18
tags: Swift
---

### 在Swift使用KVC/KVO,无效是因为什么?
KVC/KVO支持是`Objective-C`中运行时特性,Swift中使用需要两个条件
- 1.属性所在的类/监听器最终继承自`NSObject`
- 2.用@objc dynamic修饰对应的属性

示例:
```swift
// 1.继承自NSObject
class Person: NSObject {

    // 2.用@objc dynamic修饰对应的属性
    @objc dynamic var age: Int = 0
    
    var observation: NSKeyValueObservation?
    
    override init() {
        super.init()
        
        // block方式的KVO
        // 可以使用keyPath \类名.属性.属性
        observation = observe(\Person.age, options: NSKeyValueObservingOptions.new) { (person, change) in
            print(change.newValue as Any)
        }
    }
}

let p = Person()
p.age = 20

p.setValue(25, forKey: "age")
```
