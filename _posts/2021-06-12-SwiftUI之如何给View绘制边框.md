---
layout: post
title: SwiftUI之Menu示例
date: 2021-06-12
tags: SwiftUI
---

### 1.最简单的边框
```swift
Text("Hacking with Swift")
    .border(Color.green)
```
2.边框不紧挨着View
```swift
Text("Hacking with Swift")
    .padding()
    .border(Color.green)
```
3.边框宽度
```swift
Text("Hacking with Swift")
    .padding()
    .border(Color.red, width: 4)
```
4.使用overlay()复杂边框:圆角边框
```swift
Text("Hacking with Swift")
    .padding()
    .overlay(
        RoundedRectangle(cornerRadius: 16)
            .stroke(Color.blue, lineWidth: 4)
    )
```
