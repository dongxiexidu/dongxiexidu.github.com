---
layout: post
title: SwiftUI之DisclosureGroup显示隐藏内容
date: 2021-07-07
tags: SwiftUI
---

iOS 14新增DisclosureGroup,可以非常便捷的显示或隐藏内容,特别适合多段文字
### 示例
```swift
@State private var revealDetails = false

var body: some View {
    DisclosureGroup("Show Terms", isExpanded: $revealDetails) {
        Text("Long terms and conditions here long terms and conditions here long terms and conditions here long terms and conditions here long terms and conditions here long terms and conditions here.")
        
        Text("Break it down!")
        Image(systemName: "star")
    }
    .frame(width: 300)
}
```

原文:https://www.hackingwithswift.com/quick-start/swiftui/how-to-hide-and-reveal-content-using-disclosuregroup
