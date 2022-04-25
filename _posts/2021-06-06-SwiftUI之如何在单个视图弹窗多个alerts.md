---
layout: post
title: SwiftUI之如何在单个视图弹窗多个alert
date: 2021-06-06
tags: SwiftUI
---

### 1.如下代码,给VStack添加两个alert()modifier会弹窗吗?
```swift
struct ContentView: View {
    @State private var showingAlert1 = false
    @State private var showingAlert2 = false

    var body: some View {
        VStack {
            Button("Show 1") {
                showingAlert1 = true
            }
            
            Button("Show 2") {
                showingAlert2 = true
            }
        }
        .alert(isPresented: $showingAlert1) {
            Alert(title: Text("One"), message: nil, dismissButton: .cancel())
        }
        .alert(isPresented: $showingAlert2) {
            Alert(title: Text("Two"), message: nil, dismissButton: .cancel())
        }

    }
}

```
上述代码只会弹一个alert2

### 2.如何修改上述代码
```swift
struct ContentView: View {
    @State private var showingAlert1 = false
    @State private var showingAlert2 = false

    var body: some View {
        VStack {
            Button("Show 1") {
                showingAlert1 = true
            }.alert(isPresented: $showingAlert1) {
                Alert(title: Text("One"), message: nil, dismissButton: .cancel())
            }
            
            Button("Show 2") {
                showingAlert2 = true
            }.alert(isPresented: $showingAlert2) {
                Alert(title: Text("Two"), message: nil, dismissButton: .cancel())
            }
        }

    }
}
```
直接把alert绑定到button上就可以了
```swift
To solve this, you need to make sure you attach no more than one alert() modifier to each view. That might sound limiting, but remember: you don’t need to attach the alerts to the same view – you can attach them anywhere. In fact, you might find that attaching them directly to the thing that shows them (e.g. a button) works best for you.
```
[参考](https://www.hackingwithswift.com/quick-start/swiftui/how-to-show-multiple-alerts-in-a-single-view)
