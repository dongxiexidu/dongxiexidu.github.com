---
layout: post
title: SwiftUI之属性包装器@Published
date: 2021-07-03
tags: SwiftUI
---


### 1.什么是 @Published 属性包装器？
@Published是SwiftUI最有用的包装之一，允许我们创建出能够被自动观察的对象属性，SwiftUI会自动监视这个属性，一旦发生了改变，会自动修改与该属性绑定的界面。

```swift
class CalculatorModel: ObservableObject {
    @Published var brain: CalculatorBrain = .left("0")
}
```

如果不使用@Published
```swift
// mode必须是引用类型class,并且继承ObservableObject
class CalculatorModel: ObservableObject {
    let objectWillChange = PassthroughSubject<Void, Never>()

    var brain: CalculatorBrain = .left("0") {
        willSet {
            //通知外界有事件要发生了 (此处的事件即驱动 UI 的数据将要发生改变)。
            objectWillChange.send()
        }
    }
    
    var brain: CalculatorBrain = .left("0")
}
```

Combine 中存在 @Published 封装，用来把一个 class 的属性值转变为 Publisher。它同时提供了值的存储和对外的 Publisher (**通过投影符号 $ 获取**)

在被 订阅时，当前值也会被发送给订阅者，它的**底层其实就是一个 CurrentValueSubject:**

@Published更被喵神形象的称为自动驾驶!
