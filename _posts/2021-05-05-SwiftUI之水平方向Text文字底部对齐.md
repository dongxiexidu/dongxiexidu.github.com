---
layout: post
title: SwiftUI之水平方向Text文字底部对齐
date: 2021-05-05
tags: SwiftUI
---


![demo]({{ "/assets/img/bottom_align.jpeg" | absolute_url }})
### 1.示例
```swift
HStack(alignment: .firstTextBaseline) {
    Text("Current Score:")
        .font(.headline)
        .fontWeight(.semibold)
    
    Text("\(flagGame.currentScore)")
        .font(.title)
        .fontWeight(.bold)
}
.foregroundColor(.yellow)
```
使用alignment: .firstTextBaseline这个参数,如果不写,默认是两个Text垂直居中对齐
