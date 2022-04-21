---
layout: post
title: SwiftUI之属性包装器@StateObject和@ObservedObject.
date: 2021-07-02
tags: SwiftUI
---


### 1.StateObject 的概念和使用
SwiftUI 的 @StateObject 属性包装器旨在填补状态管理中的一个非常具体的空白：
当需要在一个视图中创建引用类型并确保它保持活动状态以在该视图和共享的其他视图中使用时。

例如，考虑一个简单的 User 类，如下：
```swift
class User: ObservableObject {
    var username = "@twostraws"
}
```
如果想在各种视图中使用它，需要在 SwiftUI 外部创建它并将其注入，
或者在一个 SwiftUI 视图中创建它并使用 @StateObject，如下所示：
```swift
struct ContentView: View {
    @StateObject var user = User()

    var body: some View {
        Text("Username: \(user.username)")
    }
}
```

- 这将确保 User 实例在视图更新时不会被破坏。**以前可能已经使用 @ObservedObject 来获得相同的结果，但这是有风险的。有时 @ObservedObject 可能会意外释放它正在存储的对象，因为它不是设计为最终的真相来源目的，但 @StateObject 就不会发生这种情况，因此应该改用它**。
- 在 @State 和 @ObservedObject 之间存在 @StateObject，这是 @ObservedObject 的特有版本，它们几乎是相同的工作方式：必须符合 ObservableObject 协议，可以使用 @Published 将属性标记为导致更改通知，和任何观察 @StateObject 的视图都会在对象发生变化时刷新它们的主体。
- @StateObject 和 @ObservedObject 之间有一个重要的区别，即所有权，哪个视图创建了对象，哪个视图只是在监视它。规则是这样的：首先创建对象的视图必须使用 @StateObject，告诉 SwiftUI 它是数据的所有者并负责保持它的活动，所有其它视图都必须使用 @ObservedObject 来告诉 SwiftUI，它们想要观察对象的变化但不直接拥有它。
- 每个对象应该只使用一次 @StateObject，它应该在负责创建对象的任何视图中，共享对象的所有其它视图都应使用 @ObservedObject。

### 2.如何使用 @StateObject 创建和监控外部对象？
- SwiftUI 的 @StateObject 属性包装器是 @ObservedObject 的一种特殊形式，具有所有相同的功能，但有一个重要的补充：它应该用于创建观察对象，而不仅仅是存储从外部传入的对象。
- 当使用 @StateObject 向视图添加属性时，SwiftUI 认为该视图是可观察对象的所有者，传递该对象的所有其他视图都应使用 @ObservedObject，这非常重要，如果弄错，可能会发现对象被意外破坏了，这会导致应用程序看似随机崩溃。因此明确一点：我们应该使用 @StateObject 在某处创建可观察对象，并且在传递该对象的所有后续地方应该使用 @ObservedObject。

如下所示：
```swift
// An example class to work with
class Player: ObservableObject {
    @Published var name = "Taylor"
    @Published var age = 26
}

// A view that creates and owns the Player object.
struct ContentView: View {
    // 创建的时候,必须使用@StateObject而不是@ObservedObject
    @StateObject var player = Player()

    var body: some View {
        NavigationView {
            NavigationLink(destination: PlayerNameView(player: player)) {
                Text("Show detail view")
            }
        }
    }
}

// A view that monitors the Player object for changes, but
// doesn't own it.
struct PlayerNameView: View {
    // 此处只是监听Player改变,并不拥有它
    @ObservedObject var player: Player

    var body: some View {
        Text("Hello, \(player.name)!")
    }
}
```
**总结:@StateObject用于创建复杂对象, @ObservedObject用于接收复杂对象, @State用于基本数据类型比如Int,String**
