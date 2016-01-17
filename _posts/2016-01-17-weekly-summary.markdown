---
layout: post
title: "Weekly-Summary"
date: "2016-01-17 09:20"
tag:
    - Summary
    - IOS
---
 本周完成了糖记的1.0版本的主要工作，完成了打包上传的步骤。等待apple的审核。本周还进行了其他的学习记录.
## outlets and Action
outlets 是一种特殊的OC属性可以用来执行storyboard or nib中的objects，它的keywords是IBOutlet，声明它的表达式为：
```
  @property (weak,nonatomic)IBOutlet UIButton *myButton;
```
Action 另一方面呢是可以将storyboard上的object与相应的方法进行连接。函数定义如下可见：
```
    -(IBAction)doSomething:(id)sender;
```
其中IBAction是返回值，sender是参数，可以有也可以没有，sender是指向调用这个方法的object的指针。

## 带style的String
当然在编辑labeltext的时候，我们也可以是用带style的string。如下：

```objectivec
  NSMutableAttributedString *StyleString = [[NSMutableAttribiteString alloc] initwithString:plainText]
  NSDictionary *attributes = @{
    NSFontAttributeName:[UIFont boldSystemFontOfSize:_statusLabel.font.pointSize];
  };
  NSRange nameRange = [plainText rangeOfString:title];
  [styleText setAttributes:attributes range:nameRange];
  _statusLabel.attributedText = styleText;
```

## Delegate
IOS 中大量的使用了Deleage的模式进行设计和实现，在我的理解中可以理解为回调函数。Delegate模式的优点在我的理解里是最大限度的讲系统解耦了，
在一个objectA定义了delegate的接口，另外一个objectB只要实现了相应的接口方法，当objectA有相应行为发生的时候，只要调用objectB的相应方法就ok了。

在整个app的生命周期里，app delegate 作为这个app的delegate，当app有相应的动作发生时候，app delegate里的相应方法就会被调用。相应的方法有：

```objectivec
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // Override point for customization after application launch.
    return YES;
}

- (void)applicationWillResignActive:(UIApplication *)application {
    // Sent when the application is about to move from active to inactive state. This can occur for certain types of temporary interruptions (such as an incoming phone call or SMS message) or when the user quits the application and it begins the transition to the background state.
    // Use this method to pause ongoing tasks, disable timers, and throttle down OpenGL ES frame rates. Games should use this method to pause the game.
}

- (void)applicationDidEnterBackground:(UIApplication *)application {
    // Use this method to release shared resources, save user data, invalidate timers, and store enough application state information to restore your application to its current state in case it is terminated later.
    // If your application supports background execution, this method is called instead of applicationWillTerminate: when the user quits.
}

- (void)applicationWillEnterForeground:(UIApplication *)application {
    // Called as part of the transition from the background to the inactive state; here you can undo many of the changes made on entering the background.
}

- (void)applicationDidBecomeActive:(UIApplication *)application {
    // Restart any tasks that were paused (or not yet started) while the application was inactive. If the application was previously in the background, optionally refresh the user interface.
}

- (void)applicationWillTerminate:(UIApplication *)application {
    // Called when the application is about to terminate. Save data if appropriate. See also applicationDidEnterBackground:.
}
```
