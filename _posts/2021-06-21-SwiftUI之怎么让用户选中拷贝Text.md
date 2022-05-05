---
layout: post
title: SwiftUI之怎么让用户选中拷贝Text
date: 2021-06-21
tags: SwiftUI
---

Text文本默认是不能选中拷贝的的,可以通过.textSelection()modifier with the .enabled value
```swift
VStack(spacing: 50) {
    Text("You can't touch this")

    Text("Break it down!")
        .textSelection(.enabled)
}
```

可以直接写在VStack外面
```swift
VStack(spacing: 50) {
    Text("You can't touch this")
    Text("Break it down!")
}
.textSelection(.enabled)
```

还可以这样用在List外面
```swift
List(0..<100) { index in
    Text("Row \(index)")
}
.textSelection(.enabled)
```

原文:https://www.hackingwithswift.com/quick-start/swiftui/how-to-let-users-select-text
