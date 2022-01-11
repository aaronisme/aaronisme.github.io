---
layout: post
title: "IOS-Deleopment-Summary-4"
date: "2016-01-03 22:43"
categories: Learning
tags:
    - IOS
    - Summary
---
this Week I have implement the first tab and most of second tab.add some new things to make it feel good.
these are the problems I have faced and also found the solution:
1. chart 重复显示。
root cause：update chart 的方法没有实现。
solution：

 - 实现update chart 的方法
 - re-render这个chart，这样就有个问题是chart要重新生成很多回，这样其实并不是一个最优的方案。由于时间的问题，所以暂时还是采取的这样的一种方案，随着知识的累积尝试着将update chart的方法进行实现。

```objectivec
  [self.scatterChart removeFromSuperview];
  self.scatterChart = nil;
  [self createTheScatterChart];
```
首先将chart从view上remove下来，然后制成nil，最后重新的create。
2. chart高等自适应superview高等的问题，取高等是可以用这个方法：``` view.bounds.size.height``` 由于这个高等要在autolayout计算之后取得所以在那里调用这样的方法也是同样的问题。最后是在```viewDidAppear```中进行相应方法的调用，才对的。
3. segue中指向一个被embedded到nav view中的另外一个view的解决方法。常常因为我们需要在不同的view中来传递值，所以在IOS中就只能在segue的一些方法中进行定义。常见的实在prepareForSegue中进行定义：

```objectivec
{
    if ([[segue identifier] isEqualToString:@"showItems"]) {
        ShowItemsTableViewController *destinationViewController = [segue destinationViewController];

        [destinationViewController setItems:[self itemsFromCoreData]];
    }
}
```

但是由于destinationViewController是nav bar 所以怎么才能得到nav bar 后边的真正的viewController呢？下边就是答案：

```objectivec
  UINavigationController *navController = [segue destinationViewController];
  ShowItemsTableViewController *SITViewController = (ShowItemsTableViewController *)([navController viewControllers][0]);
  [SITViewController setItems:[self itemsFromCoreData]];
```

[可以参考这个链接](http://stackoverflow.com/questions/19774583/setting-a-property-in-a-segue-with-navigation-controller-containing-another-view)
4. 将nav bar view上的color变了以后相应的embeded进去的view的header bar同样就变了（显然么）.
5. weak and Strong reference.

- If a method name starts with init, copy, mutableCopy or new, you'll receive a non-autorelease object. This is not the case here (you are using dateWithTimeIntervalSinceNow) and as a result, you'll receive an autorelease object.

- Thus, you are instantiating an autorelease object and therefore it will not be deallocated until the autorelease pool is drained. And your weak reference will not be nil-ed until the object is deallocated. The deallocation of autorelease objects happens when your app yields back to the run loop (or you explicitly create your own autorelease pool).

- you can also use a non-autorelease object (e.g. use a method whose name starts with init) and this non-autorelease object will be deallocated immediately after being set to nil.

```objectivec
  __weak NSDate * date = [[NSDate alloc] init];
  NSlog(@"%@"，date);
  // the data will be null since it is an weak variable and it will be release after the assignment.**weak reference是不被记录成为owner的所以在ARC计算的时候是不被计算的**，所以in this case date object就会被释放掉。
  /* 同样还要注意的是：If a method name starts with init, copy, mutableCopy or new, you'll receive a non-autorelease object. This is not the case here (you are using dateWithTimeIntervalSinceNow) and as a result, you'll receive an autorelease object.
  所以非init方法返回的是autorelease 的object！而init返回的是 non autorelease的object
  */

  __weak NSDate * date = [NSDate date];
  NSlog(@"%@"，date);
  //这里的date是有值的，因为没有被autorealse，
  //Thus, you are instantiating an autorelease object and therefore it will not be deallocated until the autorelease pool is drained. And your weak reference will not be nil-ed until the object is deallocated. The deallocation of autorelease objects happens when your app yields back to the run loop (or you explicitly create your own autorelease pool).
  //
  //所以当app后台运行的时候才会这个object才会被autorelease，或者这样：
  @autoreleasepool{
    __weak NSDate * date = [NSDate date];
  }
  NSlog(@"%@"，date);

  //and **You can also use a non-autorelease object (e.g. use a method whose name starts with init) and this non-autorelease object will be deallocated immediately after being set to nil**:
```
[weak/strong/autorelease object](http://stackoverflow.com/questions/24464747/ios-about-weak-and-strong-whats-the-result-should-be-and-constant-declare)
