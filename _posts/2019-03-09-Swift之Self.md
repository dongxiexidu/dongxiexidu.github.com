---
layout: post
title: Swift之Self
date: 2019-03-09
tags: Swift
---

>>'Self' is only available in a protocol or as the result of a method in a class

分割开来的话就是两个意思

- 1.Self可以用于协议(protocol)中限制相关的类型
- 2.Self可以用于类(Class)中来充当方法的返回值类型

1.Self一般用作于返回值类型,限定返回值跟方法调用者必须是同一类型(也可以作为参数类型)

```swift
protocol Runnable {
    func run() -> Self
}

class Person: Runnable {

    // requred使用在类里面,涉及到子类继承问题,如果是结构体,则会报错
    required init() {}

    func run() -> Self {
        // 使用元类型初始化对象
        type(of: self).init()
    }
}
```

2.Self 关键字用在类里,作为函数返回值类型,表示当前类
```swift
class ClassA {
    var a: Int
    var b: Int
    init(a: Int, b: Int) {
        self.a = a
        self.b = b
    }
    
    // 如果返回自己,则可以链式调用
    @discardableResult
    func setValueA(param: Int) -> Self {
        self.a = param
        return self
    }
    @discardableResult
    func setValueB(param: Int) -> ClassA {
        self.b = param
        return self
    }
}

let objA = ClassA.init(a: 11, b: 22)
// 链式调用
objA.setValueA(param: 2).setValueB(param: 5)
print("a=\(objA.a),b=\(objA.b)") // a=2,b=5
```
总结:Self关键字应用示例,**链式调用**
