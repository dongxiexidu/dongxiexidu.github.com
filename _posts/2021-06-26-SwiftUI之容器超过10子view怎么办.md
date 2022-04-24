---
layout: post
title: SwiftUI之容器超过10子view怎么办
date: 2021-06-26
tags: SwiftUI
---

### 1.如下代码会通过运行吗?
```swift
struct ContentView: View {
    var body: some View {
        VStack {
                Text("Line 1")
                Text("Line 2")
                Text("Line 3")
                Text("Line 4")
                Text("Line 5")
                Text("Line 6")
                Text("Line 7")
                Text("Line 8")
                Text("Line 9")
                Text("Line 10")
                Text("Line 11")
        }
    }
}
```
编译会报错:`Extra argument in call`
SwiftUI中的所有容器不超过10个子view
### 2.如何应对超过10个子view的情况?
```swift
struct ContentView: View {
    var body: some View {
        VStack {
            Group {
                Text("Line 1")
                Text("Line 2")
                Text("Line 3")
                Text("Line 4")
                Text("Line 5")
                Text("Line 6")
            }
    
            Group {
                Text("Line 7")
                Text("Line 8")
                Text("Line 9")
                Text("Line 10")
                Text("Line 11")
            }
        }
    }
}
```


原文:https://www.hackingwithswift.com/quick-start/swiftui/how-to-group-views-together
