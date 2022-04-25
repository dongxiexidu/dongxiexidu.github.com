---
layout: post
title: SwiftUI之动画combine transitions
date: 2021-06-22
tags: SwiftUI
---

添加或删除视图时，SwiftUI允许您使用combined（with:）方法组合变换以生成新的动画样式
### 1.示例:
```swift
struct ContentView: View {
    @State private var showDetails = false

    var body: some View {
        VStack {
            Button("Press to show details") {
                withAnimation {
                    showDetails.toggle()
                }
            }

            if showDetails {
                Text("Details go here.")
                     .transition(AnyTransition.opacity.combined(with: .slide))
            }
        }

    }
}
```
考虑到结合动画的复用性,更方便的使用,可以给AnyTransition添加扩展
```swift
extension AnyTransition {
    static var moveAndScale: AnyTransition {
        AnyTransition.move(edge: .bottom).combined(with: .scale)
    }
}

struct ContentView: View {
    @State private var showDetails = false

    var body: some View {
        VStack {
            Button("Press to show details") {
                withAnimation {
                    showDetails.toggle()
                }
            }

            if showDetails {
                Text("Details go here.")
                    .transition(.moveAndScale)
            }
        }
    }
}
```


原文:https://www.hackingwithswift.com/quick-start/swiftui/how-to-combine-transitions
