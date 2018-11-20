---
layout: post
title: Runtime实战记录Method Swizzling
date: 2015-07-18
tags: Runtime
---

## 需求:点击`textField`或者`textView`,弹窗键盘,点击`cell`跳转到另外一个控制器时候,同时退出键盘,返回到上一级界面,键盘自动弹出

默认情况键盘是不会随着页面跳转而自动退出的,如图
![default_demo.gif](https://upload-images.jianshu.io/upload_images/987457-001a46bb91cc07d6.gif?imageMogr2/auto-orient/strip)

实现的最终效果图,如下
![swizzling_demo.gif](https://upload-images.jianshu.io/upload_images/987457-d1508a3fb4f57dbc.gif?imageMogr2/auto-orient/strip)

### 实现步骤
- 1.给`UIViewController`扩展分类文件`UIViewController+Swizzling`
- 2.在`+ (void)load`方法中,`viewWillDisappear:`和`viewDidAppear:`交换方法
- 3.在`swizzling_viewWillDisappear:`中,拿到当前响应者`View`,使用`objc_setAssociatedObject`设置关联对象,并取消响应
- 4.在`swizzling_viewDidAppear`拿到设置的关联对象,设置成第一响应


```swift
+ (void)load{
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        swizzling_exchangeMethod([UIViewController class] ,@selector(viewDidAppear:), @selector(swizzling_viewDidAppear:));
        swizzling_exchangeMethod([UIViewController class] ,@selector(viewWillDisappear:), @selector(swizzling_viewWillDisappear:));
    });
}
```

```swift
// 注意这个key相当于NULL,仅仅一个key时候才可以这样使用,多个key不可以这样使用
static char UIFirstResponderViewAddress;
#pragma mark - ViewWillDisappear
- (void)swizzling_viewWillDisappear:(BOOL)animated{
    [self swizzling_viewWillDisappear:animated];
    UIView *view = (UIView *)[UIResponder currentFirstResponder];
    if ([view isKindOfClass:[UIView class]] && view.viewController == self) {
        objc_setAssociatedObject(self, &UIFirstResponderViewAddress, view, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
        [view resignFirstResponder];
    }else{
        objc_setAssociatedObject(self, &UIFirstResponderViewAddress, nil, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
    }
}
```

```swift
#pragma mark - ViewDidAppear
- (void)swizzling_viewDidAppear:(BOOL)animated{
    [self swizzling_viewDidAppear:animated];
    UIView *view = objc_getAssociatedObject(self, &UIFirstResponderViewAddress);
    [view becomeFirstResponder];
}
```
