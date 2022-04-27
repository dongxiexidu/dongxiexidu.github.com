---
layout: post
title: SwiftUI之使用fullScreenCover()展示全屏幕
date: 2021-06-16
tags: SwiftUI
---

### 1.fullScreenCover跟sheets一样从底部弹出来
注意:**它不能通过滑动手势退出弹窗**
```swift
struct FullScreenModalView: View {
    @Environment(\.presentationMode) var presentationMode

    var body: some View {
        ZStack {
            Color.primary.edgesIgnoringSafeArea(.all)
            Button("Dismiss Modal") {
                presentationMode.wrappedValue.dismiss()
            }
        }
    }
}

struct ContentView: View {
    @State private var isPresented = false

    var body: some View {
        Button("Present!") {
            isPresented.toggle()
        }
        .fullScreenCover(isPresented: $isPresented, content: FullScreenModalView.init)
    }
}
```

注意:**fullScreenCover() is not available on macOS.**
