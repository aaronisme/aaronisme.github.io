---
layout: post
title: "Keyboard不再遮挡住view"
date: "2016-02-28 09:25"
categories: Learning
tags:
  - IOS
  - Summary
---
## Keyboard不再遮挡住View
在IOS开发中常见的一个问题就是Keyboard的大小遮挡住了View的问题，那么这样的情况如何解决呢？从实现上来看就是让Constrain按照keyboard的位置进行变化就可以了。
1. 首先是 实例化一个 NSNotificationCenter 的实例。
2. 将keyboard的显示和hide注册为两个observer，同时在相应的observer中实现相应的方法例如：

```objectivec
  NSNotificationCenter *ctr = [NSNotificationCenter defaultCenter];
  [ctr addObserver:self
        selector:@selector(moveKeyboardInResponseWillShowNotification:)
            name:UIKeyboardWillShowNotification
          object:nil];

  [ctr addObserver:self
        selector:@selector(moveKeyboardInResponseWillHideNotification:)
            name:UIKeyboardWillHideNotification
          object:nil];
```
当keyboard现实和hide的时候就会调用相应方法。同时在方法上实现那两个方法就可以了。

```objectivec

-(void)moveKeyboardInResponseWillShowNotification:(NSNotification *)notification{
    NSLog(@"This is the log for show");
    NSDictionary *info= [notification userInfo];
    CGRect kbRect;
    kbRect = [[info objectForKey:UIKeyboardFrameBeginUserInfoKey] CGRectValue];
    CGFloat duration = [[info objectForKey:UIKeyboardAnimationDurationUserInfoKey] floatValue];
    UIViewAnimationCurve curve = [[info objectForKey:UIKeyboardAnimationCurveUserInfoKey] integerValue];
    [self.view layoutSubviews];


    [UIView beginAnimations:nil context:nil];
    [UIView setAnimationDuration:duration];
    [UIView setAnimationCurve:curve];
    [UIView setAnimationBeginsFromCurrentState:YES];

    self.bottomLayout.constant = kbRect.size.height;
    [self.view layoutSubviews];
    [UIView commitAnimations];
}

-(void)moveKeyboardInResponseWillHideNotification:(NSNotification *)notification{
    NSLog(@"This is the log for hide");
    NSDictionary *info= [notification userInfo];
    CGRect kbRect = CGRectZero;
    CGFloat duration = [[info objectForKey:UIKeyboardAnimationDurationUserInfoKey] floatValue];
    UIViewAnimationCurve curve = [[info objectForKey:UIKeyboardAnimationCurveUserInfoKey] integerValue];
    [self.view layoutSubviews];


    [UIView beginAnimations:nil context:nil];
    [UIView setAnimationDuration:duration];
    [UIView setAnimationCurve:curve];
    [UIView setAnimationBeginsFromCurrentState:YES];

    self.bottomLayout.constant = kbRect.size.height;
    [self.view layoutSubviews];
    [UIView commitAnimations];
}

```
注意这里最核心的一点通过notification 的userInfo那到了keyboard的大小kbRect,获得了时间duration，拿到了动画的Curve。拿到这些以后来设置这个UIView的render的参数，通过kbRect的height来设置bottom
的Constrain。
[具体的代码可以参考：][a0e0838d]

  [a0e0838d]: https://github.com/aaronisme/iostricks/tree/master/KeyboardDemo "keyboardDemo"
