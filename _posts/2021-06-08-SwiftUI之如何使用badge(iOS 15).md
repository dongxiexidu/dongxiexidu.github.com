---
layout: post
title: SwiftUI之如何使用badge(iOS 15)
date: 2021-06-08
tags: SwiftUI
---

在iOS 15 新增了badge() modifier
### 1.给tab view items 添加数字
```swift
struct ContentView: View {
    var body: some View {
        VStack {
            TabView {
                Text("Your home screen here")
                    .tabItem {
                        Label("Home", systemImage: "house")
                    }
                    .badge(5)

                Text("Your Me screen here")
                    .tabItem {
                        Label("Me", systemImage: "house")
                    }
                    .badge(1)
            }  
        }
    }
}
```
![demo]({{ "/assets/img/tab_badge.png" | absolute_url }})

### 2.在List rows里面,在右侧显示字符串,并且默认secondary color
```swift
struct ContentView: View {
    var body: some View {
        VStack {
            List {
                Text("Wi-Fi")
                    .badge("LAN Solo")

                Text("Bluetooth")
                    .badge("On")
            }
            
        }
    }
}
```

![demo]({{ "/assets/img/badge_right.png" | absolute_url }})
[参考](https://www.hackingwithswift.com/quick-start/swiftui/how-to-add-a-badge-to-tabview-items-and-list-rows)
