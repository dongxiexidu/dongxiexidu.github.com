---
layout: post
title: SwiftUI之View生命周期onAppear()和onDisappear()
date: 2021-05-03
tags: SwiftUI
---

UIKit的 `viewDidAppear()` 和 `viewDidDisappear()` 等价于SwiftUI的方法`onAppear()` 和 `onDisappear()`
### 1.示例
```swift
struct ContentView: View {
    var body: some View {
        NavigationView {
            VStack {
                NavigationLink(destination: DetailView()) {
                    Text("Hello World")
                }
            }
            .onAppear {
                print("ContentView appeared!")
            }
            .onDisappear {
                print("ContentView disappeared!")
            }
        }
    }
}

struct DetailView: View {
    var body: some View {
        VStack {
            Text("Second View")
        }
        .onAppear {
            print("DetailView appeared!")
        }
        .onDisappear {
            print("DetailView disappeared!")
        }
    }
}
```

原文:https://www.hackingwithswift.com/quick-start/swiftui/how-to-respond-to-view-lifecycle-events-onappear-and-ondisappear
