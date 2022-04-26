---
layout: post
title: SwiftUI之如何给text里面插入images
date: 2021-06-07
tags: SwiftUI
---

使用+号,直接插入,将Image当成Text初始化值
### 1.示例
```swift
Text("Hello ") + Text(Image(systemName: "star")) + Text(" World!")
```
![demo]({{ "/assets/img/hellowStar.png" | absolute_url }})
### 2.示例
```swift
(Text("Hello ") + Text(Image(systemName: "star")) + Text(" World!"))
    .foregroundColor(.blue)
    .font(.largeTitle)
```
![demo]({{ "/assets/img/blueWorld.png" | absolute_url }})
### 3.示例
```swift
Text("Goodbye ") + Text(Image(systemName: "star")) + Text(" World!")
    .foregroundColor(.blue)
    .font(.largeTitle)
```
![demo]({{ "/assets/img/goodbyeWorld.png" | absolute_url }})
[参考](https://www.hackingwithswift.com/quick-start/swiftui/how-to-insert-images-into-text)
