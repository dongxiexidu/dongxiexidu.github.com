---
layout: post
title: iOS基于ResponderChain的交互
date: 2019-01-01
tags: Runtime
---

参考了:https://casatwy.com/responder_chain_communication.html
参考了:https://github.com/qingfengiOS/MutableCellTableView

一般对象之间事件监听我们用代理,比如这种情况,控制器的子`view`(`CustomView`),上有一个btn点击事件,一般这种事件监听应该交由控制器统一处理
```swift
@protocol CustomViewDelegate <NSObject>
- (void)btnClick:(UIButton *)btn;
@end

@interface CustomView : UIView
@property (nonatomic, weak) id<CustomViewDelegate> delegate;
@end

- (IBAction)btnClick:(UIButton *)sender {
    if ([_delegate respondsToSelector:@selector(btnClick:)]) {
        [_delegate btnClick:sender];
    }
}
```

在控制器中遵守协议,控制器作为代理,然后实现代理方法
```swift
// 在控制器中遵守协议
@interface ViewController ()<CustomViewDelegate>
@end
- (void)viewDidLoad {
    [super viewDidLoad];
    
    CustomView *customV = [[NSBundle mainBundle] loadNibNamed:@"CustomView" owner:self options:nil].firstObject;
    customV.frame = CGRectMake(10, 100, [UIScreen mainScreen].bounds.size.width-10, 250);
    [self.view addSubview:customV];
    // 控制器作为代理
    customV.delegate = self;
}

// 实现代理方法
- (void)btnClick:(nonnull UIButton *)btn {
    NSLog(@"%s,parameter=%@",__func__,btn);
}
```

然而,发现一个新的思路:使用`UIResponder`实现事件响应链条传递事件,凡是继承UIResponder的控件都可以比如`UIView`,我们知道大部分控件都是直接或者间接继承自`UIView`,比如`UIButton`,`UILabel`,`UIImageView`,`UIControl`,`UISwitch`,`UIScrollView`,`UITextView`,`UITextField`,`UISearchBar`,`UIPickerView`等等

### 1.给UIResponder扩展分类
```swift
@interface UIResponder (Router)

/**
 凡是继承UIResponder的控件,都可以通过事件响应链条传递事件

 @param eventName 事件名
 @param userInfo 附加参数,nullable可以为空
 */
- (void)routerEventWithName:(NSString *)eventName userInfo:(nullable NSDictionary *)userInfo;


/**
 通过方法SEL生成NSInvocation

 @param selector 方法
 @return Invocation对象
 */
- (NSInvocation *)createInvocationWithSelector:(SEL)selector;

@end
```

实现
```swift
@implementation UIResponder (Router)

- (void)routerEventWithName:(NSString *)eventName userInfo:(nullable NSDictionary *)userInfo{
    
    NSLog(@"UIResponder---eventName=%@,userInfo=%@",eventName,userInfo);
    [[self nextResponder] routerEventWithName:eventName userInfo:userInfo];
}

- (NSInvocation *)createInvocationWithSelector:(SEL)selector{
    
    // 通过方法名创建方法签名 注意:不能使用 [[NSInvocation alloc] init]也不可以用下面这个方法
   // NSMethodSignature *signature = [NSInvocation instanceMethodSignatureForSelector:selector];

    NSMethodSignature *signature = [[self class] instanceMethodSignatureForSelector:selector];
    // 通过方法签名创建invocation
    // signature == nil 这里就会崩溃
    NSInvocation *invocation = [NSInvocation invocationWithMethodSignature:signature];
    [invocation setTarget:self];
    [invocation setSelector:selector];
    return invocation;
}
```

### 2.发送事件(子view中)
```swift
#import "UIResponder+Router.h"

- (IBAction)btnClick:(UIButton *)sender {

    [self routerEventWithName:kEventMyButtonName userInfo: @{@"btnKey": sender.currentTitle}];
}
```
### 3.响应处理事件(以控制器为示例)
```swift
#import "UIResponder+Router.h"

#pragma mark - Event Response
- (void)routerEventWithName:(NSString *)eventName userInfo:(NSDictionary *)userInfo{
    
    NSLog(@"controller---eventName=%@,userInfo=%@",eventName,userInfo);
    // 处理事件
    [self handleEventWithName:eventName parameter:userInfo];
    // 如果有需求 可以把响应链继续传递下去 和super 一样效果
    [[self nextResponder] routerEventWithName:eventName userInfo:userInfo];
    // [super routerEventWithName:eventName userInfo:userInfo];
}

// 处理事件
- (void)handleEventWithName:(NSString *)eventName parameter:(NSDictionary *)parameter{
    
    // 获取invocation对象
    NSInvocation *invocation = self.eventStrategyDictionary[eventName];

    // 设置invocation参数
    // 因为有两个隐藏参数self和_cmd,所有index从2开始
    [invocation setArgument:&parameter atIndex:2];

    // 调用方法
    [invocation invoke];
}
```

#### Strategy模式进行更好的事件处理
```swift

/// 事件策略字典 key:事件名 value:事件的invocation对象
@property (strong, nonatomic) NSDictionary *eventStrategyDictionary;

#pragma mark - Getter
- (NSDictionary <NSString *, NSInvocation *>*)eventStrategyDictionary {
    if (!_eventStrategyDictionary) {
        NSInvocation *btnInvocation = [self createInvocationWithSelector:@selector(customViewButtonClickWithParameter:)];
        NSInvocation *switchInvocation = [self createInvocationWithSelector:@selector(customViewSwitchValueChangeWithParameter:)];
        NSInvocation *imgInvocation = [self createInvocationWithSelector:@selector(customViewImageViewTapWithParameter:)];
        
        _eventStrategyDictionary = @{ kEventMyButtonName: btnInvocation,
                                      kEventMySwitchName: switchInvocation,
                                      kEventMyImageViewName: imgInvocation
                                    };

    }
    return _eventStrategyDictionary;
}

// 处理button点击事件
- (void)customViewButtonClickWithParameter:(NSDictionary *)parameter{
    
    NSLog(@"%s,parameter=%@",__func__,parameter);
}
```
使用Strategy模式，即可避免多事件处理场景下导致的冗长if-else语句

对响应链的调用打印
```swift
UIResponder---eventName=CustomSwitchEvent,userInfo={
mySwitchKey = 0;
}
UIResponder---eventName=CustomSwitchEvent,userInfo={
mySwitchKey = 0;
}
controller---eventName=CustomSwitchEvent,userInfo={
mySwitchKey = 0;
}
[ViewController customViewSwitchValueChangeWithParameter:],parameter={
mySwitchKey = 0;
}
UIResponder---eventName=CustomSwitchEvent,userInfo={
mySwitchKey = 0;
}
UIResponder---eventName=CustomSwitchEvent,userInfo={
mySwitchKey = 0;
}
AppDelegate---eventName=CustomSwitchEvent,userInfo={
mySwitchKey = 0;
}
```

由于响应链是一层一层往上传递的,最终会经过`AppDelegate`,所以如果要对所有事件做统一处理可以在`AppDelegate`这里
```swift
#import "UIResponder+Router.h"

@interface AppDelegate ()

@end

@implementation AppDelegate

- (void)routerEventWithName:(NSString *)eventName userInfo:(NSDictionary *)userInfo{
    
    NSLog(@"AppDelegate---eventName=%@,userInfo=%@",eventName,userInfo);
}
@end
```
在整个响应链中,任何响应的地方都可以处理,比如添加一些参数(可使用Decorator模式，能够更加有序地收集、归拢数据，降低了工程的维护成本),处理一些业务逻辑

如果页面比较复杂,比如商品详情页,业务逻辑非常多,我们可以新建一个文件(`EventProxy`),把所有的响应事件放到这个文件进行统一处理,这样就达到了为控制器减负,看起来有点像新的架构
>新的架构模式：MVCE（Modle View Controller Event)

### EventProxy.h的声明
```swift
@interface EventProxy : NSObject

- (void)handleEventWithName:(NSString *)eventName parameter:(NSDictionary *)parameter;

@end
```
### EventProxy.m的实现
```swift
#import "EventProxy.h"
#import "UIResponder+Router.h"

NSString *const kEventMyButtonName = @"CustomButtonEvent";
NSString *const kEventMySwitchName = @"CustomSwitchEvent";
NSString *const kEventMyImageViewName = @"CustomImageViewEvent";

@interface EventProxy ()

// 使用Strategy模式，即可避免多事件处理场景下导致的冗长if-else语句
/// 事件策略字典 key:事件名 value:事件的invocation对象
@property (strong, nonatomic) NSDictionary *eventStrategyDictionary;

@end

@implementation EventProxy

- (NSInvocation *)createInvocationWithSelector:(SEL)selector{
    
    // 通过方法名创建方法签名 注意:不能使用 [[NSInvocation alloc] init]也不可以用下面这个方法
    // NSMethodSignature *signature = [NSInvocation instanceMethodSignatureForSelector:selector];
    
    NSMethodSignature *signature = [[self class] instanceMethodSignatureForSelector:selector];
    // 通过方法签名创建invocation
    // signature == nil 这里就会崩溃
    NSInvocation *invocation = [NSInvocation invocationWithMethodSignature:signature];
    [invocation setTarget:self];
    [invocation setSelector:selector];
    return invocation;
}

#pragma mark - Getter
- (NSDictionary <NSString *, NSInvocation *>*)eventStrategyDictionary {
    if (!_eventStrategyDictionary) {
        NSInvocation *btnInvocation = [self createInvocationWithSelector:@selector(customViewButtonClickWithParameter:)];
        NSInvocation *switchInvocation = [self createInvocationWithSelector:@selector(customViewSwitchValueChangeWithParameter:)];
        NSInvocation *imgInvocation = [self createInvocationWithSelector:@selector(customViewImageViewTapWithParameter:)];
        
        _eventStrategyDictionary = @{ kEventMyButtonName: btnInvocation,
                                      kEventMySwitchName: switchInvocation,
                                      kEventMyImageViewName: imgInvocation
                                      };
        
    }
    return _eventStrategyDictionary;
}

- (void)handleEventWithName:(NSString *)eventName parameter:(NSDictionary *)parameter{
    
    // 获取invocation对象
    NSInvocation *invocation = self.eventStrategyDictionary[eventName];
    
    // 设置invocation参数
    // 因为有两个隐藏参数self和_cmd,所有index从2开始
    [invocation setArgument:&parameter atIndex:2];
    
    // 调用方法
    [invocation invoke];
}

- (void)customViewButtonClickWithParameter:(NSDictionary *)parameter{
    
    NSLog(@"具体方法实现,parameter=%@",parameter);
}
- (void)customViewSwitchValueChangeWithParameter:(NSDictionary *)parameter{
    
    NSLog(@"具体方法实现,parameter=%@",parameter);
}
- (void)customViewImageViewTapWithParameter:(NSDictionary *)parameter{
    
    NSLog(@"具体方法实现,parameter=%@",parameter);
}
```
### 控制器中的代码实现
```swift
@interface ViewController ()
@property (strong, nonatomic) EventProxy *eventProxy;
@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    // 初始化事件代理
    self.eventProxy = [[EventProxy alloc] init];
}

#pragma mark - Event Response
- (void)routerEventWithName:(NSString *)eventName userInfo:(NSDictionary *)userInfo{
    
    NSLog(@"controller---eventName=%@,userInfo=%@",eventName,userInfo);
    // 处理事件
    [self.eventProxy handleEventWithName:eventName parameter:userInfo];
    // 把响应链继续传递下去 和super 一样效果
    [[self nextResponder] routerEventWithName:eventName userInfo:userInfo];
    // [super routerEventWithName:eventName userInfo:userInfo];
}
@end
```

这样控制器把事件的处理交给`eventProxy`去处理,这样控制器是不是很清爽呢,是不是很像**MVCE（Modle View Controller Event)**

## 总结一下:
- 1.在子view中发送事件
- 2.一般在控制器中处理事件,也可以发送给一个专门对象`eventProxy`进行处理这个事件
- 3.如果有需要可以继续传给其他地方进行处理,比如父控件,`AppDelegate`

[demo下载](https://github.com/dongxiexidu/ResponderChainDemo )
