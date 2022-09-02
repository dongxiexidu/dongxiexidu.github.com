---
layout: post
title: SwiftUI之AccentColor
date: 2021-07-17
tags: SwiftUI
---


SwiftUI 2.0后,新建app在Assets.xcassets文件夹中,除了一个AppIcon,还会有一个AccentColor主题颜色,如图
![demo]({{ "/assets/img/accentColor.jpeg" | absolute_url }})


```swift
TabView { // 系统内置图片就是accentColor
    ContentView()
        .tabItem {
            Image(systemName: "square.grid.2x2")
            Text("Browse")
        }
    VideoListView()
        .tabItem {
            Image(systemName: "play.rectangle")
            Text("Watch")
        }
    MapView()
        .tabItem {
            Image(systemName: "map")
            Text("Locations")
        }
    GalleryView()
        .tabItem {
            Image(systemName: "photo")
            Text("Gallery")
        }
}
```

![demo]({{ "/assets/img/accentColor_home.png" | absolute_url }})

![demo]({{ "/assets/img/accentColor_nav.png" | absolute_url }})


**一些组件默认情况下就是AccentColor效果**
比如导航栏的返回箭头颜色, 导航栏左右侧按钮系统图片颜色
```swift
.toolbar {
    ToolbarItem(placement: .navigationBarTrailing) { // 导航栏右侧按钮
        Button(action: {
            videos.shuffle() // 数组顺序随机排序
        }) { // 使用系统图片默认就是accentColor
            Image(systemName: "arrow.2.squarepath")
        }
    }
}
```
参考项目: https://github.com/dongxiexidu/SwiftUI_Udemy
