---
layout: post
title: SwiftUI之如何代码切换TabView选中的tabItem
date: 2021-06-09
tags: SwiftUI
---

给tab view items 添加数字
```swift
struct ContentView: View {
    @State var selectedView = 1

        var body: some View {
            TabView(selection: $selectedView) {
                Button("Show Second View") {
                    selectedView = 2
                }
                .padding()
                .tabItem {
                    Label("First", systemImage: "1.circle")
                }
                .tag(1)

                Button("Show First View") {
                    selectedView = 1
                }
                .padding()
                .tabItem {
                    Label("Second", systemImage: "2.circle")
                }
                .tag(2)
            }
        }
}
```
