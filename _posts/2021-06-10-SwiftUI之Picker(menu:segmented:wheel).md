---
layout: post
title: SwiftUI之Picker(menu/segmented/wheel)
date: 2021-06-09
tags: SwiftUI
---

### 1.menu会自动显示已选择项(前面一个✔️),会根据按钮位置自动调整向上或向下弹窗
```swift
struct ContentView: View {
    @State private var selection = "Red"
    let colors = ["Red", "Green", "Blue", "Black", "Tartan"]

    var body: some View {
        VStack {
            Picker("Select a paint color", selection: $selection) {
                ForEach(colors, id: \.self) {
                    Text($0)
                }
            }
            .pickerStyle(.menu)
            .padding(.init(top: 300, leading: 0, bottom: 0, trailing: 0))

            Text("Selected color: \(selection)")
        }
    }
}
```
![demo]({{ "/assets/img/picker_menu.png" | absolute_url }})
2.wheel
![demo]({{ "/assets/img/picker_wheel.png" | absolute_url }})
3.segmented
![demo]({{ "/assets/img/picker_segmented.png" | absolute_url }})
