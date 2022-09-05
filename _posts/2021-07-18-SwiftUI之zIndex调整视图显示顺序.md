---
layout: post
title: SwiftUI之zIndex调整视图显示顺序
date: 2021-07-18
tags: SwiftUI
---


SwiftUI中一般使用ZStack容器对视图进行重叠布局,比如底部图片,图片右下角文字,又或者一个悬浮的按钮在视图的右下角

使用VStack中,对视图使用.offset也可以在两个视图中产生重叠的效果
![demo]({{ "/assets/img/zIndex.jpeg" | absolute_url }})

![demo]({{ "/assets/img/zIndexCode.jpeg" | absolute_url }})


参考项目: https://github.com/dongxiexidu/SwiftUI_Udemy 中Avocado_SwiftUI项目
参考博客: https://www.fatbobman.com/posts/zIndex/
