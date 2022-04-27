---
layout: post
title: SwiftUI之如何使用Shape绘制图案
date: 2021-06-19
tags: SwiftUI
---

1.自定义结构体遵守Shape协议
2.实现 path(in:) 方法
### 1.一样高
```swift
struct ShrinkingSquares: Shape {
    func path(in rect: CGRect) -> Path {
        var path = Path()
        // 步长为5,从1到15,遍历3次 (stride: 大步走,进展)
        for i in stride(from: 1, through: 15, by: 5.0) {
            let rect = CGRect(x: 0, y: 0, width: rect.width, height: rect.height)
            let insetRect = rect.insetBy(dx: CGFloat(i), dy: CGFloat(i))
            path.addRect(insetRect)
            path.addEllipse(in: insetRect)
        }

        return path
    }
}

struct ContentView: View {
    var body: some View {
        ShrinkingSquares()
            .stroke()
            .frame(width: 200, height: 200)
    }
}
```
原文:https://www.hackingwithswift.com/quick-start/swiftui/how-to-draw-a-custom-path
