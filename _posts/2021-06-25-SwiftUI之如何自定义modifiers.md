---
layout: post
title: SwiftUI之如何自定义modifiers
date: 2021-06-25
tags: SwiftUI
---

### 1.自定义一个通用的PrimaryLabel的modifiers
```swift
// 定义一个结构体遵守ViewModifier协议
struct PrimaryLabel: ViewModifier {
    // 该协议要求您接受一个body（content:）方法
    func body(content: Content) -> some View {
        content
            .padding()
            .background(Color.red)
            .foregroundColor(Color.white)
            .font(.largeTitle)
    }
}
```
用法示例:
```swift
struct ContentView: View {
    var body: some View {
        Text("Hello, SwiftUI")
            .modifier(PrimaryLabel())
    }
}
```

原文:https://www.hackingwithswift.com/quick-start/swiftui/how-to-create-custom-modifiers
