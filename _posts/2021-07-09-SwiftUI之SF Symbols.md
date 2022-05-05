---
layout: post
title: SwiftUI之SF Symbols
date: 2021-07-09
tags: SwiftUI
---

下载mac SF Symbols app. 地址: https://developer.apple.com/sf-symbols/

1.SwiftUI中如何加载SF Symbol图标
```swift
Image(systemName: "star")
```
2.SF Symbols 图标紧跟文字显示
```swift
Label("Please try again", systemImage: "xmark.circle")
```
3.SF Symbols 图标嵌入文字显示
```swift
Text("Please press the \(Image(systemName: "calendar")) button")
```
4.SF Symbol更改图标的大小
```swift
Image(systemName: "airplane")
    .font(.largeTitle)
    
Image(systemName: "house")
    .font(.system(size: 72))
```
5.SF Symbol更改图标的scale比例
```swift
Image(systemName: "house")
    .imageScale(.large)
```
6.SF Symbol更改图标的粗细
```swift
Image(systemName: "keyboard")
    .font(.largeTitle.weight(.black))
```

7.SF Symbol更改图标的颜色
```swift
Image(systemName: "doc")
    .foregroundColor(.red)
```

还有其他的,比如渐变,自定义...都不常用,就不一一记录了

原文:https://www.hackingwithswift.com/articles/237/complete-guide-to-sf-symbols
