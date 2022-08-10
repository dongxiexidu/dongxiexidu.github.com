---
layout: post
title: SwiftUI之Equatable, Hashable, Codable, Identifiable{
date: 2021-07-11
tags: SwiftUI
---

一般定义模型数据会遵守这几个协议
```swift
struct Task: Equatable, Hashable, Codable, Identifiable {
    let id: UUID
    var title: String
    var isDone: Bool
    
    init(title: String, isDone: Bool) {
        self.id = UUID()
        self.title = title
        self.isDone = isDone
    }
}
```

### 1.Equatable 协议要求实现 ==
```swift
public protocol Equatable {

    /// Returns a Boolean value indicating whether two values are equal.
    ///
    /// Equality is the inverse of inequality. For any values `a` and `b`,
    /// `a == b` implies that `a != b` is `false`.
    ///
    /// - Parameters:
    ///   - lhs: A value to compare.
    ///   - rhs: Another value to compare.
    static func == (lhs: Self, rhs: Self) -> Bool
}
```

### 2.Hashable 协议要求结构体存储的属性，都是可哈希的
```swift
public protocol Hashable : Equatable {

    /// The hash value.
    ///
    /// Hash values are not guaranteed to be equal across different executions of
    /// your program. Do not save hash values to use during a future execution.
    ///
    /// - Important: `hashValue` is deprecated as a `Hashable` requirement. To
    ///   conform to `Hashable`, implement the `hash(into:)` requirement instead.
    var hashValue: Int { get }

    /// Hashes the essential components of this value by feeding them into the
    /// given hasher.
    ///
    /// Implement this method to conform to the `Hashable` protocol. The
    /// components used for hashing must be the same as the components compared
    /// in your type's `==` operator implementation. Call `hasher.combine(_:)`
    /// with each of these components.
    ///
    /// - Important: Never call `finalize()` on `hasher`. Doing so may become a
    ///   compile-time error in the future.
    ///
    /// - Parameter hasher: The hasher to use when combining the components
    ///   of this instance.
    func hash(into hasher: inout Hasher)
}
```
对于class须要手动遵守Hashable 协议
```swift
class Person: Hashable {
    var age:Int
    var name:String
    init(age:Int, name:String) {
        self.age = age
        self.name = name
    }
    
    //因为`Hashable : Equatable`,所以必须要写实现 == 
    static func == (lhs: Person, rhs: Person) -> Bool {
        lhs.age == rhs.age && lhs.name == rhs.name
    }
    
    func hash(into hasher: inout Hasher) {
        hasher.combine(age)
        hasher.combine(name)
    }
}
```

### 3.Codable
```swift
public typealias Codable = Decodable & Encodable

public protocol Encodable {
    func encode(to encoder: Encoder) throws
}
public protocol Decodable {
    init(from decoder: Decoder) throws
}
```
- Codable 是Encodable 和Decodable 两个协议的组合
- Encodable：用在那些需要被编码的类型上
- Decodable：表示那些能够被解码的类型
一般JSON 转换为Model使用


### 4.Identifiable
```swift
@available(macOS 10.15, iOS 13.0, watchOS 6.0, tvOS 13.0, *)
public protocol Identifiable {

    /// A type representing the stable identity of the entity associated with
    /// an instance.
    associatedtype ID : Hashable

    /// The stable identity of the entity associated with this instance.
    var id: Self.ID { get }
}
```
其实Identifiable 非常简单实用，主要作用就是作为一个对象的唯一标识。

在swiftUI中,使用forEach遍历数组需要一个id作为唯一的标识符
```swift
struct ExpenseItem {
    let name: String
    let type: String
}

var testArray: [ExpenseItem] = [ExpenseItem.init(name: "li", type: "1"), ExpenseItem.init(name: "di", type: "2")]

// 可以把属性name作为id
ForEach(testArray, id: \.name) { item in
    
}
```

如果遵守了Identifiable
```swift
struct ExpenseItem: Identifiable {
    var id = UUID()
    let name: String
    let type: String
}
```
就可以省略掉臃肿的写法,可以这样写
```
ForEach(testArray) { item in
    
}
```

总结: **因为`Hashable : Equatable`,所以如果遵守了Hashable,那么没必要在额外加Equatable**


参考SwiftUI 之 Equatable协议:https://zhuanlan.zhihu.com/p/447406100
