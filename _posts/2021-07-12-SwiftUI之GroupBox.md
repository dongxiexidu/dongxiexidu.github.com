---
layout: post
title: SwiftUI之GroupBox
date: 2021-07-12
tags: SwiftUI
---

GroupBox布局控件,默认背景灰色,小圆角,字如其名,一般展示一组内容

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
