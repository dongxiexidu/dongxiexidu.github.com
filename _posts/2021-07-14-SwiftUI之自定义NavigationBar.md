---
layout: post
title: SwiftUI之自定义NavigationBar
date: 2021-07-14
tags: SwiftUI
---


```swift
extension Home {
    var navBar: some View {
        
        HStack {
            Button(action: {}) {
                Image(systemName: "line.horizontal.3.decrease")
                    .font(.system(size: 24, weight:
                            .heavy))
                    .foregroundColor(.primary)
            }
            Spacer()
            Text("DrinkIt")
                .font(.title)
                .fontWeight(.heavy)
                .foregroundColor(.primary)
            Spacer()
            
            Button(action: {}) {
                Image(systemName: "bag")
                    .font(.system(size: 24, weight:
                            .heavy))
                    .foregroundColor(.primary)
            }
        }
    }
}
```
或者使用ZStack容器
```swift
// MARK: NavBar
extension Detail {
    private var navBar: some View {

        ZStack {
            HStack {
                Button(action: {
                    withAnimation(Animation.easeIn(duration: 0.5)){
                        start.toggle()
                    }
                }) {
                    Image(systemName: "arrow.left")
                        .font(.system(size: 24, weight:
                                        .heavy))
                        .foregroundColor(.white)
                }

                Spacer()

                Button(action: {}) {
                    Image(systemName: "bag")
                        .font(.system(size: 24, weight:
                                        .heavy))
                        .foregroundColor(.white)
                }

            }

            Text("DrinkIt")
                .font(.title)
                .fontWeight(.heavy)
                .foregroundColor(.primary)
        }
    }

}
```
![demo]({{ "/assets/img/navigationBar.png" | absolute_url }})

参考: https://github.com/safarsafarov/DrinkIt-SwiftUI项目
