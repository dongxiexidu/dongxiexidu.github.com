---
layout: post
title: SwiftUI之Identifiable协议
date: 2021-06-04
tags: SwiftUI
---

### 1.Identifiable定义
```swift
A class of types whose instances hold the value of an entity with stable identity.

@available(macOS 10.15, iOS 13.0, watchOS 6.0, tvOS 13.0, *)
public protocol Identifiable {

    /// A type representing the stable identity of the entity associated with
    /// an instance.
    associatedtype ID : Hashable

    /// The stable identity of the entity associated with this instance.
    var id: Self.ID { get }
}
```
一类类型，其实例持有具有稳定标识的实体的值。

```swift
/// Use the `Identifiable` protocol to provide a stable notion of identity to a 
/// class or value type. For example, you could define a `User` type with an `id`
/// property that is stable across your app and your app's database storage. 
/// You could use the `id` property to identify a particular user even if other 
/// data fields change, such as the user's name.
///
/// `Identifiable` leaves the duration and scope of the identity unspecified.
/// Identities can have any of the following characteristics:
///
/// - Guaranteed always unique, like UUIDs.
/// - Persistently unique per environment, like database record keys.
/// - Unique for the lifetime of a process, like global incrementing integers.
/// - Unique for the lifetime of an object, like object identifiers.
/// - Unique within the current collection, like collection indices.
///
/// It's up to both the conformer and the receiver of the protocol to document
/// the nature of the identity.
///
/// Conforming to the Identifiable Protocol
/// =======================================
///
/// `Identifiable` provides a default implementation for class types (using
/// `ObjectIdentifier`), which is only guaranteed to remain unique for the
/// lifetime of an object. If an object has a stronger notion of identity, it
/// may be appropriate to provide a custom implementation.
```
其实Identifiable 协议非常简单实用，主要作用就是作为一个对象的唯一标识。

### 2示例
```swift
struct ExpenseItem {
    let id: UUID()
    let name: String
    let type: String
    let amount: Int
}
```
我们遍历他,需要一个唯一标识
```swift
ForEach(expenses.items, id: \.id) { item in
    Text(item.name)
}
```
用Identifiable就不用这么麻烦了

```swift
struct ExpenseItem: Identifiable {
    // 协议要求必须添加
    let id = UUID()
    let name: String
    let type: String
    let amount: Int
}

ForEach(expenses.items) { item in
    Text(item.name)
}
```

参考:https://www.hackingwithswift.com/books/ios-swiftui/working-with-identifiable-items-in-swiftui
