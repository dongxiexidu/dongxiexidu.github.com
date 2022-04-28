---
layout: post
title: SwiftUI之使两个View一样宽/高
date: 2021-06-18
tags: SwiftUI
---

1.在view上添加frame(maxWidth: .infinity) 或 frame(maxHeight: .infinity)
2.给两个view的容器添加 fixedSize() modifier
### 1.一样高
```swift
HStack {
    Text("This is a short string.")
        .padding()
        .frame(maxHeight: .infinity)
        .background(Color.red)

    Text("This is a very long string with lots and lots of text that will definitely run across multiple lines because it's just so long.")
        .padding()
        .frame(maxHeight: .infinity)
        .background(Color.green)
}
.fixedSize(horizontal: false, vertical: true)
.frame(maxHeight: 200)
```
### 2.一样宽
```swift
VStack {
    Button("Log in") { }
        .foregroundColor(.white)
        .padding()
        .frame(maxWidth: .infinity)
        .background(Color.red)
        .clipShape(Capsule()) // 圆角

    Button("Reset Password") { }
        .foregroundColor(.white)
        .padding()
        .frame(maxWidth: .infinity)
        .background(Color.red)
        .clipShape(Capsule()) // 圆角
}
.fixedSize(horizontal: true, vertical: false)
```
注:capsule 胶囊

原文:https://www.hackingwithswift.com/quick-start/swiftui/how-to-make-two-views-the-same-width-or-height
