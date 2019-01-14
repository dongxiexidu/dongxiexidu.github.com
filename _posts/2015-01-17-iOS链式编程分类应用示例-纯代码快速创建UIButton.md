---
layout: post
title: iOS链式编程分类应用示例-纯代码快速创建UIButton
date: 2015-01-17
tags: 链式编程 Extension/分类
---
### 给`UIButton`扩展分类,添加自己封装的方法和属性
```swift
#import <UIKit/UIKit.h>

NS_ASSUME_NONNULL_BEGIN

@interface UIButton (Ext)

// 写成属性是有提示的
@property (copy, nonatomic,readonly) UIButton *(^imageName)(NSString *aName);
@property (copy, nonatomic,readonly) UIButton *(^title)(NSString *aName);
@property (copy, nonatomic,readonly) UIButton *(^titleFont)(NSUInteger aNumber);
@property (copy, nonatomic,readonly) UIButton *(^textColor)(UIColor *aColor);
@property (copy, nonatomic,readonly) UIButton *(^btnBackgroundColor)(UIColor *aColor);
@property (copy, nonatomic,readonly) UIButton *(^btnframe)(CGFloat x, CGFloat y, CGFloat w, CGFloat h);
@property (copy, nonatomic,readonly) UIButton *(^addTarget)(id target, SEL action, UIControlEvents controlEvents);

// 该方法为工厂方法,能够快速创建一个ChainButton,在一个参数为block的方法中一次性设置好你需要的ChainButton
+ (UIButton *)dx_makeButton:(void (^)(UIButton *))block;

// 这样写是没有提示的
//- (DXButton *(^)(NSString *aName))imageName;
//- (DXButton *(^)(NSString *aName))title;
//- (DXButton *(^)(NSUInteger aNumber))titleFont;
//- (DXButton *(^)(UIColor *aColor))textColor;
//- (DXButton *(^)(CGFloat x, CGFloat y, CGFloat w, CGFloat h))btnframe;

@end

NS_ASSUME_NONNULL_END
```

```swift
#import "UIButton+Ext.h"

@implementation UIButton (Ext)

- (UIButton *(^)(NSString *aName))imageName{
    
    return ^(NSString *aName) {
        if (aName.length > 0) {
            [self setImage:[UIImage imageNamed:aName] forState:UIControlStateNormal];
        }
        return self;
    };
}

- (UIButton *(^)(NSString *aName))title{
    return ^(NSString *aName) {
        [self setTitle:aName forState:UIControlStateNormal];
        return self;
    };
}

- (UIButton *(^)(NSUInteger aNumber))titleFont {
    
    return ^(NSUInteger aNumber) {
        self.titleLabel.font = [UIFont systemFontOfSize:aNumber];
        return self;
    };
}
- (UIButton *(^)(UIColor *aColor))textColor {
    
    return ^(UIColor *aColor) {
        [self setTitleColor:aColor forState:UIControlStateNormal];
        return self;
    };
}

- (UIButton *(^)(UIColor *))btnBackgroundColor{
    return ^(UIColor* color){
        self.backgroundColor= color;
        return self;
    };
}

- (UIButton *(^)(CGFloat x, CGFloat y, CGFloat w, CGFloat h))btnframe{
    
    return ^(CGFloat x, CGFloat y, CGFloat w, CGFloat h) {
        self.frame = CGRectMake(x, y, w, h);
        return self;
    };
}
- (UIButton *(^)(id target, SEL action, UIControlEvents controlEvents))addTarget{
    return ^(id target, SEL action, UIControlEvents controlEvents) {
        [self addTarget:target action:action forControlEvents:controlEvents];
        return self;
    };
}

// 该方法为工厂方法,能够快速创建一个ChainButton,在一个参数为block的方法中一次性设置好你需要的ChainButton
+ (UIButton *)dx_makeButton:(void (^)(UIButton *))block{
    UIButton *btn = [[UIButton alloc] init];
    block(btn);
    return btn;
}

@end
```

### 使用示例:
```swift
UIButton *button = [UIButton dx_makeButton:^(UIButton * _Nonnull btn) {
    btn.title(@"船长").imageName(@"a").textColor([UIColor yellowColor]).titleFont(22);
    btn.btnBackgroundColor([UIColor lightGrayColor]);
    btn.btnframe(100, 100, 100, 50);
    btn.addTarget(self, @selector(btnClickAction), UIControlEventTouchUpInside);
}];
[self.view addSubview:button];
```
总结:如果使用属性,只需要getter方法,不需要setter方法,因此属性使用了`readonly`,block当做属性使用,书写时候会有参数提示
```swift
btn.titleFont(<#NSUInteger aNumber#>)
```
