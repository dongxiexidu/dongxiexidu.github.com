---
layout: post
title: SwiftUI之动画animation
date: 2021-06-24
tags: SwiftUI
---

SwiftUI有两种方法可以为其视图层次结构的更改设置动画：`animation（）`和`withAnimation（）`。
它们在不同的地方使用，但都有平滑应用程序中视图变化的效果。

**animation（）方法用于绑定，它要求SwiftUI为导致绑定值被修改的任何更改设置动画**
### 1.animation（）示例:
```swift
struct ContentView: View {
    @State private var showingWelcome = false

    var body: some View {
        VStack {
            Toggle("Toggle label", isOn: $showingWelcome.animation())

            if showingWelcome {
                Text("Hello World")
            }
        }
    }
}
```
控制动画类型,默认是 `fade animation`
```swift
Toggle("Toggle label", isOn: $showingWelcome.animation(.spring()))
```

使用常规状态而不是绑定时，可以通过在withAnimation（）调用中包装更改来设置更改的动画
### 2.withAnimation()示例:
```swift
struct ContentView: View {
    @State private var showingWelcome = false

    var body: some View {
        VStack {
            Button("Toggle label") {
                withAnimation(.spring()) {
                    showingWelcome.toggle()
                }
            }
            
            if showingWelcome {
                Text("Hello World")
            }
        }
    }
}
```
注意:**这两个结合动画是同时进行的,不分先后顺序**
原文:https://www.hackingwithswift.com/quick-start/swiftui/swiftui-tips-and-tricks
