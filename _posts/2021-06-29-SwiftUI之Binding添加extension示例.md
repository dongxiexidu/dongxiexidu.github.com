---
layout: post
title: SwiftUI之Binding添加extension示例
date: 2021-06-29
tags: SwiftUI
---

### 1.监听Slider值的改变示例
```swift
struct ContentView: View {
    @State private var rating = 0.0

    var body: some View {
        Slider(value: $rating)
            .onChange(of: rating) { value in
                print("Rating changed to \(value)")
            }
    }
}
```

换一个思路:给Binding添加一个extension,在extension里面自定义一个方法,返回一个Binding,重写set方法,监听wrppedValue值
```swift
extension Binding {
    func onChange(_ handler: @escaping (Value) -> Void) -> Binding<Value> {
        Binding(
            get: { self.wrappedValue },
            set: { newValue in
                self.wrappedValue = newValue
                handler(newValue)
            }
        )
    }
}
```

应用
```swift
struct ContentView: View {
    @State private var rating = 0.0

    var body: some View {
        Slider(value: $rating.onChange(sliderChanged))
    }

    func sliderChanged(_ value: Double) {
        print("Rating changed to \(value)")
    }
}
```

原文:https://www.hackingwithswift.com/articles/224/common-swiftui-mistakes-and-how-to-fix-them
