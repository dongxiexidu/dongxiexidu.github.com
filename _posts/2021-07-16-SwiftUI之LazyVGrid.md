---
layout: post
title: SwiftUI之LazyVGrid
date: 2021-07-16
tags: SwiftUI
---


LazyVGrid和LazyHGrid相当于UICollection

```swift
struct MainGridContentView: View {
    
    @StateObject var viewModel = MainGridContentViewModel()
    
    // spacing 垂直网格中的视图之间(水平方向)默认存在一个间距15
    // 定义的网格是多少列
//    let columns = [GridItem(.flexible(), spacing: 5),// 水平方向与next item 间距 5
//                   GridItem(.flexible(), spacing: 25), // 水平方向与next item 间距 25
//                   GridItem(.flexible(), spacing: 30)] // 水平方向与next item 间距 30, 因为水平方向没有next item 因此写space是无效的
   
//    let columns = [GridItem(.flexible()),
//                   GridItem(.flexible()),
//                   GridItem(.flexible())]

// 固定尺寸    
//    let columns = [GridItem(.fixed(120)),
//                   GridItem(.fixed(150)),
//                   GridItem(.fixed(180))]
//
    // .adaptive不指定具体几列,自适应
    let columns = [GridItem(.adaptive(minimum: 100, maximum: 120))]
    
    var body: some View {
        NavigationView {
            ScrollView {
                LazyVGrid(columns: columns, spacing: 8) { // spacing: 垂直方向间距, 默认值大约是8 ?
                    ForEach(MockData.avatars, id: \.id) { avatar in
                        AvatarView(avatar: avatar,
                                   width: .infinity,
                                   height: 90, font: .callout)
                        //.background(Color.gray)
                        .onTapGesture {
                            viewModel.selectedAvatar = avatar
                        }
                    }
                }
            }
            .navigationTitle(Text("🤺 Avatars"))
        }
    }
}
```

示例图
![demo]({{ "/assets/img/lazyVGrid.png" | absolute_url }})

参考项目: https://github.com/dongxiexidu/SwiftUI-LazyVGrid
