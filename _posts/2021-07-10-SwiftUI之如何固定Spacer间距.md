---
layout: post
title: SwiftUI之如何固定Spacer间距
date: 2021-07-10
tags: SwiftUI
---


使用frame() modifier
```swift
VStack {
    Text("First Label")
    Spacer()
        .frame(height: 50)
    Text("Second Label")
    Spacer()
        .frame(minHeight: 50, maxHeight: 500)
    Text("Third Label")
}
```
最小间距,如果有间距尽可能大
```swift
VStack {
    Text("First Label")
    Spacer(minLength: 50)
    Text("Second Label")
}
```

原文:https://www.hackingwithswift.com/quick-start/swiftui/how-to-make-a-fixed-size-spacer
