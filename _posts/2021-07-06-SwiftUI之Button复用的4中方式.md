---
layout: post
title: SwiftUI之Button复用的4中方式
date: 2021-07-06
tags: SwiftUI
---


### 1.extension
```swift
extension Button {
    func primaryStyle() -> some View {
        self
            .padding()
            .foregroundColor(.white)
            .clipShape(Capsule())
        }
}
```

### 2.自定义ViewModifier
```swift
struct PrimaryButton: ViewModifier {
    // 该协议要求您接受一个body（content:）方法
    func body(content: Content) -> some View {
        content
            .padding()
            .foregroundColor(.white)
            .clipShape(Capsule())
    }
}
```

### 3.使用ViewBuilder自定义View
```swift
struct PrimaryButton<Content: View>: View {
    let content: Content
    init(@ViewBuilder content: () -> Content) {
        self.content = content()
    }
    var body: some View {
        content
            .padding()
            .foregroundColor(.white)
            .clipShape(Capsule())
    }
}
```

### 4.自定义ButtonStyle
```swift
struct PrimaryButtonStyle: ButtonStyle {
    func makeBody(configuration: Configuration) -> some View {
        configuration.label
            .padding()
            .foregroundColor(.white)
            .clipShape(Capsule())
    }
}

//使用示例:
.buttonStyle(PrimaryButtonStyle())
```
