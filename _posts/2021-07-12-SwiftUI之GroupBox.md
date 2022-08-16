---
layout: post
title: SwiftUI之GroupBox
date: 2021-07-12
tags: SwiftUI
---

GroupBox布局控件,默认背景灰色,小圆角,字如其名,一般展示一组内容

### 示例一:
```swift
struct SourceLinkView: View {
    var body: some View {
        GroupBox() {
            HStack {
                Text("Content source")
                Spacer()
                Link("Wikipedia", destination: URL(string: "https://wikipedia.com")!)
                Image(systemName: "arrow.up.right.square")
            }
                .font(.footnote)
        }
            .padding(.top, 10)
            .padding(.bottom, 40)
    }
}
```
![demo]({{ "/assets/img/groupBox.jpeg" | absolute_url }})

### 示例二:
```swift
//MARK: Section 1
GroupBox(label: SettingsLabelView(labelText: "Fruits", icon: "info.circle")
) {
    Divider().padding(.vertical, 4)
    HStack(alignment: .center, spacing: 10) {
        Image("logo")
            .resizable().scaledToFit()
            .frame(width: 80, height: 80)
            .cornerRadius(10)
        Text("Most Fruits are naturally low in fat, sodium and carlories. None have cholesterol. Fruits are sources of many essential nutrients, including potassium, dietary fiber, vitamins and much more.")
            .font(.footnote)
    }
}

//MARK: Section 3
GroupBox {

    SettingsRowView(title: "Version", content: "1.1.0 ")
} label: {
    SettingsLabelView(labelText: "Application", icon: "apps.iphone")
}
```
![demo]({{ "/assets/img/groupBox2.png" | absolute_url }})
