---
layout: post
title: Swift 如何实现类似kingfisher点语法1
date: 2018-08-29
categories: test
tags: kingfisher  
---

### 实现需求:
"123ccc456".dx.numberCount 打印里面有多少个是数字?  123 456 一共6个数字

本质就是创建一个第三方对象`DX`,让这个对象去实现这个计算属性`numberCount`,字符串.dx便初始化了这个对象,并把它自己(字符串)传递给了这个对象`DX`

1.给字符串添加一个dx属性(计算属性)
```swift
extension String {
    var dx: DX { return DX(self) }
}
```

2.声明一个类`DX`,给这个类添加一个计算属性
```swift
struct DX {
    
    var string: String
    public init(_ string: String){
        self.string = string
    }
    
    var numberCount: Int {
        var count = 0
        for c in string where ("0"..."9").contains(c) {
            count += 1
        }
        return count
    }
}
```
小结: 实现.dx.属性就是创建一个临时对象,并把自己传递给这个临时对象,让这个临时对象去实现相应的方法或者计算属性
