---
layout: post
title: SwiftUI之给View添加模糊效果
date: 2021-06-17
tags: SwiftUI
---

### 1.使用blue()给图片添加高斯模糊
```swift
Image("apple")
    .resizable()
    .scaledToFill()
    .ignoresSafeArea(.all)
    .blur(radius: 20)
```


### 2.iOS 15: Materials
SwiftUI has a brilliantly simple equivalent to UIVisualEffectView, that combines ZStack, the background() modifier, and a range of built-in materials.
SwiftUI有一个相当和UIVisualEffectView的非常类似的功能
```swift
ZStack {
    Image("singapore")

    Text("Visit Singapore")
        .padding()
        .background(.thinMaterial)
}
```

你可以调整模糊类型
- .ultraThinMaterial
- .thinMaterial
- .regularMaterial
- .thickMaterial
- .ultraThickMaterial

如果使用的是次前景色，SwiftUI会自动调整文本颜色，使其具有鲜明的效果，并能从背景中脱颖而出：
```swift
ZStack {
    Image("singapore")

    Text("Visit Singapore")
        .foregroundColor(.secondary)
        .padding()
        .background(.ultraThinMaterial)
}
```

原文:https://www.hackingwithswift.com/quick-start/swiftui/how-to-add-visual-effect-blurs
参考:https://stackoverflow.com/questions/56610957/is-there-a-method-to-blur-a-background-in-swiftui
