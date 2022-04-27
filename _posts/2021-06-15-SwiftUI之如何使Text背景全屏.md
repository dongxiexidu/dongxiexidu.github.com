---
layout: post
title: SwiftUI之如何使Text背景全屏
date: 2021-06-15
tags: SwiftUI
---

### 1.给View添加一个extension
```swift
extension View {
    func expandable () -> some View {
        ZStack {
            Color.clear
            self
        }
    }
}
// 使用示例
struct ContentView: View {
    var body: some View {
        Text("Hello, World!")
        .expandable()
        .background(Color.orange)
    }
}
```
