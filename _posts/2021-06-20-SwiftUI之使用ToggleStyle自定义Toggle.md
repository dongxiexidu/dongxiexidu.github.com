---
layout: post
title: SwiftUI之使用ToggleStyle自定义Toggle
date: 2021-06-20
tags: SwiftUI
---

示例:
```swift
struct CheckToggleStyle: ToggleStyle {
    func makeBody(configuration: Configuration) -> some View {
        Button {
            configuration.isOn.toggle()
        } label: {
            Label {
                configuration.label
            } icon: {
                Image(systemName: configuration.isOn ? "checkmark.circle.fill" : "circle")
                    .foregroundColor(configuration.isOn ? .accentColor : .secondary)
                    .accessibility(label: Text(configuration.isOn ? "Checked" : "Unchecked"))
                    .imageScale(.large)
            }
        }
        .buttonStyle(PlainButtonStyle())
    }
}

struct ContentView: View {
    @State private var isOn = false

    var body: some View {
        Toggle("Switch Me", isOn: $isOn)
            .toggleStyle(CheckToggleStyle())
    }
}
```

修改选中✔️号颜色,比如红色.accentColor给为.red

原文:https://www.hackingwithswift.com/quick-start/swiftui/customizing-toggle-with-togglestyle
