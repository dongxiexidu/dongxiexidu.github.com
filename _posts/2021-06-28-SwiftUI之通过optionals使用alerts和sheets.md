---
layout: post
title: SwiftUI之通过optionals使用alerts和sheets
date: 2021-06-28
tags: SwiftUI
---

### 1.一般情况使用alert需要额外声明一个变量来绑定alert用以是否弹窗
```swift
struct User: Identifiable {
    let id: String
}

struct ContentView: View {
    @State private var selectedUser: User?
    @State private var showingAlert = false

    var body: some View {
        VStack {
            Button("Show Alert") {
                selectedUser = User(id: "@twostraws")
                showingAlert = true
            }
        }
        .alert(isPresented: $showingAlert) {
            Alert(title: Text("Hello, \(selectedUser!.id)"))
        }
    }
}
```

实际上,如果是否弹窗是optionals,我们可以使用另外一个方法
```swift
.alert(item: Binding<Identifiable?>, content: (Identifiable) -> Alert)
```
**绑定对象有值才弹窗**
应用示例:
```swift
struct ContentView: View {
    @State private var selectedUser: User?

    var body: some View {
        VStack {
            Button("Show Alert") {
                selectedUser = User(id: "@twostraws")
            }
        }
        .alert(item: $selectedUser) { user in
            Alert(title: Text("Hello, \(user.id)"))
        }
    }
}
```

原文:https://www.hackingwithswift.com/articles/224/common-swiftui-mistakes-and-how-to-fix-them
