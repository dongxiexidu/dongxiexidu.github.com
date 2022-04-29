---
layout: post
title: SwiftUI之@ViewBuilder
date: 2021-07-05
tags: SwiftUI
---


### 1.什么是@ViewBuilder?
从字面意思去理解 ViewBuilder 就是视图构建，其主要使用场景就是构建视图。
在Apple的官方文档中有这样一句话
```swift
// 允许闭包中提供多个子视图
allowing those closures to provide multiple child views 
```
在系统框架中,View定义有@ViewBuilder
```swift
public protocol View {
    associatedtype Body : View
    @ViewBuilder var body: Self.Body { get }
}
```

### 2.使用@ViewBuilder自定义视图
```swift
struct RedBackgroundAndCornerView<Content: View>: View {
    let content: Content
    
    init(@ViewBuilder content: () -> Content) {
        self.content = content()
    }
    
    var body: some View {
        content
            .background(Color.red)
            .cornerRadius(5)
    }
}

RedBackgroundAndCornerView {
    Text("Hello World")
}
```

可以添加存储变量
```swift
struct RedBackgroundAndCornerView<Content: View>: View {
    let content: Content
    @State var needHidden: Bool = false
    
    init(@ViewBuilder content: () -> Content) {
        self.content = content()
    }
    
    var body: some View {
        content
            .background(Color.red)
            .cornerRadius(5)
            .opacity(needHidden ? 0.0 : 1.0)
            .onTapGesture {
                self.needHidden = true
        }
    }
}
```

这种自定义视图的模式非常像设计模式的装饰器模式

>>装饰器模式（Decorator Pattern）允许向一个现有的对象添加新的功能，同时又不改变其结构。这种类型的设计模式属于结构型模式，它是作为现有的类的一个包装。

>>这种模式创建了一个装饰类，用来包装原有的类，并在保持类方法签名完整性的前提下，提供了额外的功能。

参考:https://juejin.cn/post/6844904160609959944
