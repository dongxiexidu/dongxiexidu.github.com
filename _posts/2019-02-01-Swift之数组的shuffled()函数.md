---
layout: post
title: Swift之数组的shuffled()函数
date: 2019-02-01
tags: Swift
---
shuffle 洗牌的意思

1.定义
```swift
/// Returns the elements of the sequence, shuffled.
///
/// For example, you can shuffle the numbers between `0` and `9` by calling
/// the `shuffled()` method on that range:
///
///     let numbers = 0...9
///     let shuffledNumbers = numbers.shuffled()
///     // shuffledNumbers == [1, 7, 6, 2, 8, 9, 4, 3, 5, 0]
///
/// This method is equivalent to calling `shuffled(using:)`, passing in the
/// system's default random generator.
///
/// - Returns: A shuffled array of this sequence's elements.
///
/// - Complexity: O(*n*), where *n* is the length of the sequence.
```

就是通过这个shuffled()方法,把数组中的元素随机打乱
