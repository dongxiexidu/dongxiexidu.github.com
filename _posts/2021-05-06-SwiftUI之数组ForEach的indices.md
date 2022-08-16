---
layout: post
title: SwiftUI之数组ForEach的indices
date: 2021-05-06
tags: SwiftUI
---

**需求:用ForEach遍历,需要用到索引i**

indices  [ˈɪndɪsiːz] index 的复数
### 1.示例
```swift
// 元祖数组
private let FAQ = [
    (
        question: "如何把大象装进冰箱？",
        answer: "第一，先把冰箱打开。第二，把大象装进去。第三，把冰箱门关上。"
    ),
    (
        question: "如何把企鹅装进冰箱？",
        answer: "第一，先把冰箱打开。第二，把大象拿进去。第三，把企鹅装进去。第三，把冰箱门关上。"
    ),
    (
        question: "动物森林要举行动物大会，有一只动物缺席了，是什么动物？",
        answer: "企鹅，因为它在冰箱里面。"
    )
]

// 通过数组FAQ.indices就可以拿到index
ForEach(FAQ.indices, id: \.self) { index in
    
}
```
