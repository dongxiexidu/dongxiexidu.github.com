---
layout: post
title: SwiftUI之使用无效ranges动态添加view
date: 2021-06-05
tags: SwiftUI
---

### 1.点击button,view会动态添加吗?
```swift
struct ContentView: View {
    @State private var rowCount = 4
    
    var body: some View {
        VStack {
            
            List(0..<rowCount) { row in
                Text("Row \(row)")
            }
            
            Button("Add Row") {
                rowCount += 1
            }
            .padding(.top)
        }
    }
}
```
上述代码在xcode编译会报警告`Non-constant range: argument must be an integer literal`

```swift
To fix this, you should always either use the Identifiable protocol or provide a specific id parameter of your own, to make it clear to SwiftUI this range will change over time:
```
明确指定使用SwiftUI的Identifiable协议提供一个指定的id参数
```swift
List(0..<rowCount, id: \.self) { row in
    Text("Row \(row)")
}
```

[参考](https://www.hackingwithswift.com/books/ios-swiftui/working-with-identifiable-items-in-swiftui)
