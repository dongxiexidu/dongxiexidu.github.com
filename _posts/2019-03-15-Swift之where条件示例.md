---
layout: post
title: Swift之where条件
date: 2019-03-15
tags: Swift
---

### 一.switch中使用
```swift
var data = (10, "jack")

switch data {
case let (age, _) where age > 10:
    print(data.1, "age>10")
case let (age, _) where age > 0:
    print(data.1, "age>0")
default:
    break
}
```

### 二.if中使用
```swift
let ages = [10, 20, 33, 44, 55]

for age in ages where age > 20 {
    print(age)
}
```

### 三.泛型函数中/协议中/协议扩展中

```swift
protocol Stackable {
    associatedtype Element
}

// 协议中使用where
protocol Container {
    associatedtype Stack: Stackable where Stack.Element: Equatable
}

// 泛型函数中使用where
func equal<T1: Stackable, T2: Stackable>(s1: T1, s2: T2)-> Bool
    where T1.Element == T2.Element, T2.Element: Hashable
{
    return false
}

// 协议扩展中使用where
extension Container where Self.Stack.Element: Hashable {
    
}
```
