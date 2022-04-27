---
layout: post
title: SwiftUI之stroke()和strokeBorder()区别
date: 2021-06-13
tags: SwiftUI
---

### 1.stroke()会使View变大,因为绘制边框一半在里面,一半在外面
you’ll see the circle looks bigger because the stroke is drawn half inside and half outside the circle.
```swift
Circle()
    .stroke(Color.blue, lineWidth: 50)
    .frame(width: 200, height: 200)
    .padding()
```

### 2.strokeBorder()完全在View内部绘制
Because that uses strokeBorder(), the 50-point blue stroke will be drawn entirely inside the circle.
```swift
Circle()
    .stroke(Color.blue, lineWidth: 50)
    .frame(width: 200, height: 200)
    .padding()
```

原文:https://www.hackingwithswift.com/quick-start/swiftui/how-to-draw-a-border-inside-a-view
