---
layout: post
title: SwiftUI之Menu示例
date: 2021-06-11
tags: SwiftUI
---

### 1.最简单的Menu
```swift
struct ContentView: View {
    var body: some View {
        Menu("Options") {
            Button("Order Now", action: placeOrder)
            Button("Adjust Order", action: adjustOrder)
            Button("Cancel", action: cancelOrder)
        }
    }

    func placeOrder() { }
    func adjustOrder() { }
    func cancelOrder() { }
}
```
![demo]({{ "/assets/img/menu_normal.jpeg" | absolute_url }})
注意:**On macOS, Menu is automatically rendered as a pulldown button**


2.Menu嵌套Menu
```swift
struct ContentView: View {
    var body: some View {
        Menu("Options") {
            Button("Order Now", action: placeOrder)
            Button("Adjust Order", action: adjustOrder)
            Menu("Advanced") {
                Button("Rename", action: rename)
                Button("Delay", action: delay)
            }
            Button("Cancel", action: cancelOrder)
        }
    }

    func placeOrder() { }
    func adjustOrder() { }
    func rename() { }
    func delay() { }
    func cancelOrder() { }
}
```
![demo]({{ "/assets/img/menu_nest1.jpeg" | absolute_url }})
![demo]({{ "/assets/img/menu_nest2.jpeg" | absolute_url }})

3.自定义Menu标签
```swift
struct ContentView: View {
    var body: some View {
        Menu {
            Button("Order Now", action: placeOrder)
            Button("Adjust Order", action: adjustOrder)
        } label: {
            Label("Options", systemImage: "paperplane")
        }
    }

    func placeOrder() { }
    func adjustOrder() { }
}
```
![demo]({{ "/assets/img/menue_custom.jpeg" | absolute_url }})

4.iOS 15新特性,Menu单击触发一个primary action,长按触发另一个action
```swift
struct ContentView: View {
    var body: some View {
        Menu("Options") { // 长按触发
            Button("Order Now", action: placeOrder)
            Button("Adjust Order", action: adjustOrder)
            Button("Cancel", action: cancelOrder)
        } primaryAction: {
            justDoIt()
        }
    }

    func justDoIt() { // 单击触发
        print("Button was tapped")
    }

    func placeOrder() { }
    func adjustOrder() { }
    func cancelOrder() { }
}
```
