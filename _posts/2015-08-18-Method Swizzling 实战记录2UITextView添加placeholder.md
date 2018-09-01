---
layout: post
title: Method Swizzling 实战记录2UITextView添加placeholder
date: 2015-08-18
tags: Method Swizzling runtime
---

**场景需求:**
- 1.给`UITextView`添加`placeholder`
- 2.给`UITextView`添加`placeholderLabel`
- 3.给`UITextView`添加`textValue`

**实现步骤:**
- 1.给`UITextView`扩展分类
- 2.在分类里添加三个属性
- 3.重写属性的`setter`和`getter`方法,自定义一个`label`添加到`UITextView`,根据需求隐藏和显示
- 4.监听是否需要显示`placeholder `

```swift
@interface UITextView (NTES)
@property (nonatomic, strong) NSString* placeholder;
@property (nonatomic, strong) UILabel * placeholderLabel;
@property (nonatomic, strong) NSString* textValue;
@end
```


```swift
-(void)checkIfNeedToDisplayPlaceholder{
    // If our UITextView is empty, display our Placeholder label (if we have one)
    if (self.placeholderLabel == nil)
        return;
    
    self.placeholderLabel.hidden = (![self.text isEqualToString:@""]);
}

-(void)onTap{
    // When the user taps in our UITextView, we'll see if we need to remove the placeholder text.
    [self checkIfNeedToDisplayPlaceholder];
    
    // Make the onscreen keyboard appear.
    [self becomeFirstResponder];
}

-(void)keyPressed:(NSNotification*)notification{
    //  The user has just typed a character in our UITextView (or pressed the delete key).
    //  Do we need to display our Placeholder label ?
    [self checkIfNeedToDisplayPlaceholder];
}
```

### textValue的setter/getter方法
```swift
-(void)setTextValue:(NSString *)textValue{
    //  Change the text of our UITextView, and check whether we need to display the placeholder.
    self.text = textValue;
    [self checkIfNeedToDisplayPlaceholder];
}
-(NSString*)textValue{
    return self.text;
}
```

### 通过运行时关联对象,设置placeholder
```swift
NSString const *kKeyPlaceHolder = @"kKeyPlaceHolder";
-(void)setPlaceholder:(NSString *)_placeholder{
    //  Sets our "placeholder" text string, creates a new UILabel to contain it, and modifies our UITextView to cope with
    //  showing/hiding the UILabel when needed.
    objc_setAssociatedObject(self, &kKeyPlaceHolder, (id)_placeholder, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
    
    self.placeholderLabel = [[UILabel alloc] initWithFrame:CGRectMake(8, 8, 0, 0)];
    self.placeholderLabel.numberOfLines = 1;
    self.placeholderLabel.text = _placeholder;
    self.placeholderLabel.textColor = UI_PLACEHOLDER_TEXT_COLOR;
    self.placeholderLabel.backgroundColor = [UIColor clearColor];
    self.placeholderLabel.userInteractionEnabled = true;
    self.placeholderLabel.font = self.font;
    [self addSubview:self.placeholderLabel];
    
    [self.placeholderLabel sizeToFit];
    
    //  Whenever the user taps within the UITextView, we'll give the textview the focus, and hide the placeholder if necessary.
    [self addGestureRecognizer:[[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(onTap)]];
    
    //  Whenever the user types something in the UITextView, we'll see if we need to hide/show the placeholder label.
    [[NSNotificationCenter defaultCenter] addObserver:self selector: @selector(keyPressed:) name:UITextViewTextDidChangeNotification object:nil];
    
    [self checkIfNeedToDisplayPlaceholder];
}
-(NSString*)placeholder{
    //  Returns our "placeholder" text string
    return objc_getAssociatedObject(self, &kKeyPlaceHolder);
}
```

### 通过运行时关联对象,设置placeholderLabel
```swift
NSString const *kKeyLabel = @"kKeyLabel";
-(void)setPlaceholderLabel:(UILabel *)placeholderLabel{
    //  Stores our new UILabel (which contains our placeholder string)
    objc_setAssociatedObject(self, &kKeyLabel, (id)placeholderLabel, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
    
    [[NSNotificationCenter defaultCenter] addObserver:self selector: @selector(keyPressed:) name:UITextViewTextDidChangeNotification object:nil];
    
    [self checkIfNeedToDisplayPlaceholder];
}
-(UILabel*)placeholderLabel{
    //  Returns our new UILabel
    return objc_getAssociatedObject(self, &kKeyLabel);
}
```

@dynamic 关键字没有必要,因为.h并没有发生警告
```swift
// 告诉编译器、xxx属性的setter、getter方法由开发者自己来生成
//@dynamic placeholder;
//@dynamic placeholderLabel;
//@dynamic textValue;
```

>1、@property有两个对应的词，一个是@synthesize，一个是@dynamic,如果@synthesize和@dynamic都没写，那么默认的就是@syntheszie var = _var;

>2、@synthesize的语义是如果你没有手动实现setter方法和getter方法，那么编译器会自动为你加上这两个方法。

>3、@dynamic告诉编译器,属性的setter与getter方法由用户自己实现，不自动生成。（当然对于readonly的属性只需提供getter即可）。假如一个属性被声明为@dynamic var，然后你没有提供@setter方法和@getter方法，编译的时候没问题，但是当程序运行到instance.var =someVar，由于缺setter方法会导致程序崩溃；或者当运行到 someVar = var时，由于缺getter方法同样会导致崩溃。编译时没问题，运行时才执行相应的方法，这就是所谓的动态绑定。

[iOS分类不能添加属性原因的探索](https://www.jianshu.com/p/935142af6a47)
