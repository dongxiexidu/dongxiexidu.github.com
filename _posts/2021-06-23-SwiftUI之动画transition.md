---
layout: post
title: SwiftUI之动画transition
date: 2021-06-24
tags: SwiftUI
---

### 1.示例:
```swift
struct ContentView: View {
    @State private var showDetails = false

    var body: some View {
        VStack {
            Button("Press to show details") {
                withAnimation {
                    showDetails.toggle()
                }
            }

            if showDetails {
                // Moves in from the bottom
                Text("Details go here.")
                    .transition(.move(edge: .bottom))

                // Moves in from leading out, out to trailing edge.
                Text("Details go here.")
                    .transition(.slide)

                // Starts small and grows to full size.
                Text("Details go here.")
                    .transition(.scale)
            }
        }
    }
}
```
withAnimation()默认是 fade animation,如果你想改变动画可以给一个view绑定modifier transition()

注意:**transition()必须在animation或withAnimation动画下才有效,单独的transition()不能做动画效果**

原文:https://www.hackingwithswift.com/quick-start/swiftui/how-to-add-and-remove-views-with-a-transition
