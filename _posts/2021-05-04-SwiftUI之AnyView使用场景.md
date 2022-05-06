---
layout: post
title: SwiftUI之AnyView使用场景
date: 2021-05-04
tags: SwiftUI
---

SwiftiUI 提供了一个结构体 `AnyView`来表示任意一个 `View` 实例，和 Any 一样可以用来抹除具体的类型。
### 1.示例
```swift
    private var conversionView: some View {
        switch currentConversionSet {
        case .btc:
            return AnyView(BitcoinConversionView())
        case .temperature:
            return AnyView(TemperatureConversionView())
        }
    }
```
这里如果不使用AnyView会报错:

Function declares an opaque return type, but the return statements in its body do not have matching underlying types

函数声明了一个不透明的返回类型，但其返回的主体中的语句没有匹配的基础类型

问题就出在前面的关键字 some 上。**加了 some 后协议透明，编译器在编译时推断具体代码的实际返回类型，因此要求必须只能有一个确定的类型。**

这就是 AnyView 的使用场景了，抹掉具体 View 类型，对外有一个统一的类型

总结:**AnyView好像是对some的瞒天过海**

参考:https://juejin.cn/post/6844903918409875470
