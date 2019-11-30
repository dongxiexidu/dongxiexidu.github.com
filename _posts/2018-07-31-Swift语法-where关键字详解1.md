---
layout: post
title: Swift语法-where关键字详解1
date: 2018-07-31
tags: Swift关键字
---

在Swift语法里where关键字的作用跟SQL的where一样， 即**附加条件判断**

1、 在集合遍历时使用where， 条件为真时执行代码块， 为假时不执行代码块。
```swift
let array = [0, 1, 2, 3, 4, 5, 6]

//使用switch遍历
array.forEach {
    switch $0 {
    case let x where x > 3:   //where相当于判断条件
        print("后半段")
    default:
        print("默认值")
    }
}

//使用for in遍历
for value in array where value > 2 {
    print(value)   //输出3 4 5 6
}

for (index, value) in array.enumerated() where index > 2 && value > 3 {
    print("下标:\(index), 值：\(value)")
}
```

2、在补充异常的do/catch里使用
![20171201101745372.png](https://upload-images.jianshu.io/upload_images/987457-1068b01ec70f6796.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3、 协议使用where， 只有基类实现了当前协议才能添加扩展。 换个说法， 多个类实现了同一个协议，该语法根据类名分别为这些类添加扩展， 注意是分别(以类名区分)！！！
```swift
protocol SomeProtocol {
    func someMethod()
}
 
class Person: SomeProtocol {
    let a = 1
    
    func someMethod() {
       print("call someMethod")
    }
}
 
class Student {
    let a = 2
}
 
//基类Person继承了SomeProtocol协议才能添加扩展
extension SomeProtocol where Self: Person {
    func showParamA() {
        print(self.a)
    }
}
//反例，不符合where条件
extension SomeProtocol where Self: Student {
    func showParamA() {
        print(self.a)
    }
}
let objA = Person()
let objB = Student()  //类B没实现SomeProtocol， 所有没有协议方法

objA.showParamA()  //输出1
```
**小结： where关键字可以用在集合遍历、switch/case、协议中； Swift3时if let和guard场景的where已经被Swift4的逗号取代， 例如 if let a=param, a>10(前者需要判断是否为nil，后者相当于where条件)**

原文:[Swift语法-where关键字详解](https://blog.csdn.net/brycegao321/article/details/78683676)
