---
layout: post
title: SwiftUI之适配四个平台iOS&macOS&tvOS&watchOS
date: 2021-06-27
tags: SwiftUI
---

### 1.给View添加一个extension,分别给四个平台添加一个方法
```swift
extension View {
    func iOS<Content: View>(_ modifier: (Self) -> Content) -> some View {
        #if os(iOS)
        return modifier(self)
        #else
        return self
        #endif
    }
}

extension View {
    func macOS<Content: View>(_ modifier: (Self) -> Content) -> some View {
        #if os(macOS)
        return modifier(self)
        #else
        return self
        #endif
    }
}

extension View {
    func tvOS<Content: View>(_ modifier: (Self) -> Content) -> some View {
        #if os(tvOS)
        return modifier(self)
        #else
        return self
        #endif
    }
}

extension View {
    func watchOS<Content: View>(_ modifier: (Self) -> Content) -> some View {
        #if os(watchOS)
        return modifier(self)
        #else
        return self
        #endif
    }
}
```

应用示例:
```swift
Text("Hello World")
    .iOS { $0.padding(10) }
```


原文:https://www.hackingwithswift.com/quick-start/swiftui/swiftui-tips-and-tricks
