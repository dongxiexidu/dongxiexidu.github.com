---
layout: post
title: Swift之CustomStringConvertible关键字
date: 2019-03-07
tags: Swift
---

### 如何实现自定义对象的打印字符串?

1.定义一个自定义对象的类
```swift
class Person {
    var age: Int
    var name: String
    
    init(age: Int, name: String) {
        self.age = age
        self.name = name
    }
}
```

打印这个对象
```swift
let person = Person.init(age: 11, name: "Jack")
print(person) // 返回的是类名 TEST_Swift.Person
```

2.那么如何自定义对象/实例的打印,让其打印出对象的属性值?或者说自定义打印,想打印啥就打印啥? 
目的:可以自定义输出的参数,这样在调试的时候就会方便很多

只需要遵守协议`CustomStringConvertible`或`CustomDebugStringConvertible`,实现`description` property,或`debugDescription` property
```swift
class Person: CustomStringConvertible,CustomDebugStringConvertible {
    var description: String {
        "age=\(age), name=\(name)"
    }
    
    var debugDescription: String {
        "debug age=\(age), name=\(name)"
    }
    
    var age: Int
    var name: String
    
    init(age: Int, name: String) {
        self.age = age
        self.name = name
    }
}

let person = Person.init(age: 11, name: "Jack")

print(person) // age=11, name=Jack
print(person.description) // age=11, name=Jack
print(person.debugDescription) // debug age=11, name=Jack
```
在Objective-C中也有同样的功能,充分说明了Swift面向协议开发的编程语言

一般使用扩展,写起来更优雅美观,结构更清晰
```swift
//实现 CustomStringConvertible 协议，方便输出调试
extension Person: CustomStringConvertible {
    var description: String {
        "age=\(age), name=\(name)"
    }
}
```

3.查看协议`CustomStringConvertible`定义,很简单,甚至有个示例作为描述
```swift
public protocol CustomStringConvertible {

    /// A textual representation of this instance.
    ///
    /// Calling this property directly is discouraged. Instead, convert an
    /// instance of any type to a string by using the `String(describing:)`
    /// initializer. This initializer works with any type, and uses the custom
    /// `description` property for types that conform to
    /// `CustomStringConvertible`:
    ///
    ///     struct Point: CustomStringConvertible {
    ///         let x: Int, y: Int
    ///
    ///         var description: String {
    ///             return "(\(x), \(y))"
    ///         }
    ///     }
    ///
    ///     let p = Point(x: 21, y: 30)
    ///     let s = String(describing: p)
    ///     print(s)
    ///     // Prints "(21, 30)"
    ///
    /// The conversion of `p` to a string in the assignment to `s` uses the
    /// `Point` type's `description` property.
    var description: String { get }
}
```
总结: **方便输出调试对象**
