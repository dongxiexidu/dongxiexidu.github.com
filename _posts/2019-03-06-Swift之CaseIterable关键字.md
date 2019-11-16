---
layout: post
title: Swift之CaseIterable关键字
date: 2019-03-06
tags: Swift
---

### CaseIterable  是协议中的关键字,Swift 4.2 新特性

1.定义
```swift
public protocol CaseIterable {

    /// A type that can represent a collection of all values of this type.
    associatedtype AllCases : Collection where Self == Self.AllCases.Element

    /// A collection of all values of this type.
    static var allCases: Self.AllCases { get }
}
```

理解: Iterable: 可迭代的 case: 事例 
让枚举遵守CaseIterable协议,可以实现遍历枚举值**说白了,这个关键字仅仅适用于枚举协议**

示例
```swift
enum Season: CaseIterable {
    case spring, summer, autumn, winter 
}

// season类似于数组 合成枚举:allCases,这个是自动合成特性
let season = Season.allCases
print(season.count) // 4

for season in seasons {
    print(season) // spring summer  autumn winter
}

// 编译器合成 CaseIterable 是使用了哪个具体的 Collection 类型呢？
print(type(of:Season.allCases)) // Array<Season>
```


2.如果有关联值的枚举,是不会自动合成allCases,需要我们手动实现`allCases`
```swift
enum MartialStatus : CaseIterable {
    case single
    case married(spouse: String)
    
    // 手动实现 allCases计算属性
    static var allCases: [MartialStatus] {
        return [.single, .married(spouse: "Leon")]
    }
}
```
3.某个case在某种情况下不可用,也需要自己手动实现`allCases`
```swift
enum MartialStatus : CaseIterable {
    @available(*, unavailable)
    case single
    case married

    static var allCases: [MartialStatus] { return [.married]}
}

let status = MartialStatus.allCases
print(status.count) // 1
for status in statuses {
    print(status) // married
}
```

![demo]({{ "/assets/img/deadCycle.png" | absolute_url }})

这样子写编译器会警告,但是可以通过编译,不会报错,实际上仔细看报错就知道这是个死循环
