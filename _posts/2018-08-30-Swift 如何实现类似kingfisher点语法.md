---
layout: post
title: Swift 如何实现类似kingfisher点语法imageView.kf.setImage
date: 2018-08-30
categories: test
tags: kingfisher  
---

1.声明一个类`RoundCorner `
```swift
public final class RoundCorner<Base> {
    
    public let base : Base
    public init(_ base : Base){
        self.base = base
    }
}
```

2.声明一个空协议,协议名`RoundCornerCompatible`
```swift
public protocol RoundCornerCompatible {

}
```
3.在协议扩展中,添加一个只读属性`dx`,返回一个`RoundCorner `对象
```swift
public extension RoundCornerCompatible {
    public var dx : RoundCorner<Self> {
        get { return RoundCorner(self) }
    }
}
```

4.将所需要实现`.dx`语法的Base类遵守`RoundCornerCompatible`
(凡是遵守这个协议`RoundCornerCompatible`的类,便拥有了.dx这个属性)
```swift
extension UIView : RoundCornerCompatible {}
```

到现在为止,凡是UIView的子类,便可以使用`.dx`,而这个`.dx`实际上就是`RoundCorner`这个实例对象,下面给这个对象添加自定义的方法
5.在`RoundCorner`的扩展中,添加定义的方法
```swift
extension RoundCorner where Base: UIView{
    /// 设置一个四角圆角
    ///
    /// - Parameters:
    ///   - radius: 圆角半径
    ///   - cornerColor: 圆角背景色
    public func roundCorner(radius: CGFloat,cornerColor: UIColor)  {
        
        base.layer.dx_roundCorner(radius: radius, cornerColor: cornerColor)
    }
    
    /// 设置一个普通圆角
    ///
    /// - Parameters:
    ///   - radius: 圆角半径
    ///   - cornerColor: 圆角背景色
    ///   - corners: 圆角位置
    public func roundCorner(radius: CGFloat,cornerColor: UIColor,corners: UIRectCorner) {
        base.layer.dx_roundCorner(radius: radius, cornerColor: cornerColor, corners: corners)
    }
    
    ///  设置一个带边框的圆角
    ///
    /// - Parameters:
    ///   - radius: 圆角半径
    ///   - cornerColor: 圆角背景色
    ///   - corners: 圆角位置
    ///   - borderColor: 边框颜色
    ///   - borderWidth: 边框线宽
    func roundCorner(radii: CGSize,cornerColor: UIColor, corners: UIRectCorner, borderColor: UIColor, borderWidth: CGFloat)  {
        base.layer.dx_roundCorner(cornerRadii: radii, cornerColor: cornerColor, corners: corners, borderColor: borderColor, borderWidth: borderWidth)
    }
}
```

### 使用示例

```swift
let circle = UIView()
circle.dx.roundCorner(radius: 15/2, cornerColor: color)

label.dx.roundCorner(radius: 20/2, cornerColor: UIColor.white,corners: [.topLeft,.topRight, .bottomRight, .bottomLeft])
```

[demo下载示例](https://github.com/dongxiexidu/UIViewRoundCorner)

[参考文章:Kingfisher学习笔记](https://www.jianshu.com/p/470944c81e28)
