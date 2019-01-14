---
layout: post
title: iOS UITextField实现输入手机号时自动添加空格
date: 2015-06-18
tags: UITextField 代理/协议
---

### 场景需求:
为了优化用户体验,我们往往会在让用户输入手机号码时添加空格,比如:151 6558 1234.那么在iOS中如何实现呢?

备注:当到第四位或第九位时,如果此时是正在输入,则自动增加空格,如果正在删除,则自动删除空格!!!
当到第13位时,截取前面的13位字符串,收起键盘

### 实现方法
方法一:不推荐
iOS中的输入框给UITextField添加UIControlEventEditingChanged事件 ,该方法实现输入框文字变动时的监听:textFieldDidEditing:

```swift
NSInteger i;//定义全局变量

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view from its nib.
    i = 0;
    
    [self.textField addTarget:self action:@selector(textFieldDidEditing:) forControlEvents:UIControlEventEditingChanged];
}
-(void)textFieldDidEditing:(UITextField *)textField{
    if (textField == self.textField) {
        if (textField.text.length > i) {
            if (textField.text.length == 4 || textField.text.length == 9 ) {//输入
                NSMutableString * str = [[NSMutableString alloc ] initWithString:textField.text];
                [str insertString:@" " atIndex:(textField.text.length-1)];
                textField.text = str;
            }if (textField.text.length >= 13 ) {//输入完成
                textField.text = [textField.text substringToIndex:13];
                [textField resignFirstResponder];
            }
            i = textField.text.length;
            
        }else if (textField.text.length < i){//删除
            if (textField.text.length == 4 || textField.text.length == 9) {
                textField.text = [NSString stringWithFormat:@"%@",textField.text];
                textField.text = [textField.text substringToIndex:(textField.text.length-1)];
            }
            i = textField.text.length;
        }
    }
}
```

若想要获取输入的手机,需要先删除空格;
```swift
NSString *textFieldStr =[self.textField.textstringByReplacingOccurrencesOfString:@" "withString:@""];
```

#### 方法二(推荐)

1.设置textField代理
2.遵守UITextFieldDelegate协议
3.实现代理方法`shouldChangeCharactersInRange`

```swift
-(BOOL)textField:(UITextField *)textField shouldChangeCharactersInRange:(NSRange)range replacementString:(NSString *)string {

    NSString *text = [textField text];
    NSCharacterSet *characterSet = [NSCharacterSet characterSetWithCharactersInString:@"0123456789\b"];
    string = [string stringByReplacingOccurrencesOfString:@" " withString:@""];
    // 禁止输入非0-9的其他字符,比如字母abc , 表情
    if ([string rangeOfCharacterFromSet:[characterSet invertedSet]].location != NSNotFound) {
        return NO;
    }
    text = [text stringByReplacingCharactersInRange:range withString:string];
    text = [text stringByReplacingOccurrencesOfString:@" " withString:@""];

    // 如果是电话号码格式化，需要添加这三行代码
    NSMutableString *temString = [NSMutableString stringWithString:text];
    [temString insertString:@" " atIndex:0];
    text = temString;
    NSString *newString = @"";
    while (text.length > 0) {
        NSString *subString = [text substringToIndex:MIN(text.length, 4)];
        newString = [newString stringByAppendingString:subString];
        if (subString.length == 4) {
            newString = [newString stringByAppendingString:@" "];
        }
        text = [text substringFromIndex:MIN(text.length, 4)];
    }
    newString = [newString stringByTrimmingCharactersInSet:[characterSet invertedSet]];
    if (newString.length >= 14) {
        return NO;
    }
    [textField setText:newString];
    return NO;
}
```

**总结:复制粘贴过来后同样实现了格式化, 禁止输入非0-9的其他字符**


```swift
func textField(_ textField: UITextField, shouldChangeCharactersIn range: NSRange, replacementString string: String) -> Bool {
        // 获取输入的文本，移除向输入框中粘贴文本时，系统自动加上的空格（iOS11上有该问题）
        let new = string.replacingOccurrences(of: " ", with: "")
        // 获取编辑前的文本
        var old = NSString(string: textField.text ?? "")
        // 获取编辑后的文本
        old = old.replacingCharacters(in: range, with: new) as NSString
        // 获取数字的字符集
        let number = CharacterSet(charactersIn: "0123456789")
        // 判断编辑后的文本是否全为数字
        if old.rangeOfCharacter(from: number.inverted).location == NSNotFound {
            // number.inverted表示除了number中包含的字符以外的其他全部字符
            // 如果old中不包含其他字符，则格式正确
            // 允许本次编辑
            textField.text = old as String
            // 移动光标的位置
            DispatchQueue.main.async {
                let beginning = textField.beginningOfDocument
                let position = textField.position(from: beginning, offset: range.location + new.count)!
                textField.selectedTextRange = textField.textRange(from: position, to: position)
            }
        }
        return false
    }
```
