---
layout: post
title: "IOS-summary-CoreData&Notification"
date: "2015-12-28 19:57"
tags:
  - IOS
  - summary
---

**Question1**:
In method application:handleActionWithIdentifier:forLocalNotification:completionHandler:
the **completioHandler** have to  be called in the end. but why is that? what did it do?

 In the article it is said.It’s great that you’re provided this – it means that you can perform asynchronous operations without the system killing your process when you’re in the background.(当你的app在后台运行的时候，可以进行异步操作)。
[link]https://www.shinobicontrols.com/blog/ios8-day-by-day-day-25-notification-actions





## IOS CoreData
IOS中内置了SQLite的数据库，进行数据的保存。同时提供了CoreData的ORM机制，使得可以不写任何的SQL语句，进行数据库操作。下面就是些总结内容：
![CoreDataStack](/img/in-post/ios/coreData.png)
从上面的图可以看出，coreDataStack 分为三层，最底层用于定于model的关系，实际上是各个表的外键的关系，以及用SQLite实现了数据的保存。
第二层用于保存数据与SQL语句的交互，最上一层用于与object进行交互。

使用CoreData首先在创建工程的时候，选取使用coredata的选项，在自动生成的xxx.xcdatamodeld中进行定义。关于数据和表的对于关系可以参考下面的图：
![Alt text](/img/in-post/ios/iosORM.png)
注意要定义好relationship，中的1对多和，1对1的关系。当做好这些定义之后，创建每个Entity的Class Name。点击Editor选择Create NSManageObject Subclass，这样Xcode 为你创建好了相应的Class。

同时在AppDelegate中定义好了相应的方法。
###如何使用？
在AppDelegate中的- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions方法中：

```objectivec
  [self managedObjectModel];
  [self persistentStoreCoordinator];
  NSManagedObjectContext *moc = [self managedObjectContext];
```

对于与上面的三层构架，对应可三个方法，我们平时进行交互的就是moc 也就是NSManageObjectContent。同时可以定于相应的Object的Create方法例如：

```objectivec
  -(ChroeMO *) createChroeMO{
      NSManagedObjectContext *moc = [self managedObjectContext];
      ChroeMO * chroeMO = [NSEntityDescription insertNewObjectForEntityForName:@"Chroes" inManagedObjectContext:moc];
      return chroeMO;
  }

```

进行数据交互时候的步骤：

```objectivec
	-(void)updateLogList{
	//create NSManagedObjectContext
    NSManagedObjectContext *moc = self.appDelegate.managedObjectContext;
    //create the request
    NSFetchRequest *request = [NSFetchRequest fetchRequestWithEntityName:@"ChroeLog"];

    NSError *error = nil;
    //sent the request
    NSArray *result = [moc executeFetchRequest:request error:&error];
    if(!result){
        NSLog(@"Error fetching Person objects %@\n%@",[error localizedDescription], [error userInfo]);
    }

    NSMutableString *buffer = [NSMutableString stringWithString:@""];
    for (ChroelogMO *c in result) {
        [buffer appendFormat:@"%@ at%@ %@\n",c.person_who_did_it,c.when,c.chore_done,nil];

    }
    self.persistedData.text = buffer;

}
```
有写操作的时候，完成后要保存。

```objectivec
	- (IBAction)deleteTaped:(id)sender {
    NSManagedObjectContext *moc = self.appDelegate.managedObjectContext;
    NSFetchRequest *request = [NSFetchRequest fetchRequestWithEntityName:@"ChroeLog"];

    NSError *error = nil;
    NSArray *result = [moc executeFetchRequest:request error:&error];
    if(!result){
        NSLog(@"Error fetching Person objects %@\n%@",[error localizedDescription], [error userInfo]);
    }

    for (ChroelogMO *c in result) {
        [moc deleteObject:c];
    }
    //save after changed
    [self.appDelegate saveContext];
    [self updateLogList];

}
```
