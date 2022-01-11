---
layout: post
title: "IOS-Development-Summary"
date: "2015-12-15 21:44"
categories: Learning
tags:
  - IOS
---

1 you should alloc init when using the object in OC,even when you are using the property. e.g:
```
@property(nonatomic) BSRecord *record;
```
you define the property in the viewController h file.
the record do exist when you init the viewController but it just an point which is point the record instance, so you should alloc init the instance when you using it. like:
```
self.record = [[BSRecord alloc] init];
```
otherwise you just all the point the actual instance doesn’t exist.

2, Using the datasource which is an delegate the create the TableView.
In IOS development, MVC pattern are using to create the app. so when create the tableview, there is two parts to consider,first is the tableview and second is the tableview controller, because the delegate pattern. after the tableview loaded to ask its delegator:viewController for the data. so in this case,you should implement these methods in the tableview controller to get the data for view, there are two methods to implement:

```
- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {

    return 1;
}
// num of section in table view tell how many sections in the table view

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {
#warning Incomplete implementation, return the number of rows
    return [self.allRecords count];
}
// how many rows in the one section

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"RecordsPrototypeCell" forIndexPath:indexPath];
    return cell;
}
// implement each cell,if you using the prototype you should define the identifier in attribute of the cell.
```

3,Define the unwind segue to get to the previous view,To create an unwind segue you should add the action method to the destination view controller(the view controller you would to go):

```
-(IBAction)unwindToList:(UIStoryboardSegue*)segue;
```
take the UIStoryboardSegue as the parameter;
you can link the button to the exit and choose the defined the segue. P94 in the book start developing iOS today.
using the segue to go to another view.

4, Saving and Loading data to the file

```
- (NSString *)documentsDirectory{
    NSArray *paths = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES);
    NSString *documentsDirectory = [paths firstObject];

    return documentsDirectory;
}

// get the directory of the document

- (NSString *)dataFilePath{
    return [[self documentsDirectory] stringByAppendingPathComponent:@"bsrecords.plist"];
}
// get the date file path
- (void)saveRecords{
    NSMutableData *data = [[NSMutableData alloc] init];
    NSKeyedArchiver *archiver=[[NSKeyedArchiver alloc] initForWritingWithMutableData:data];

    [archiver encodeObject:self.allRecords forKey:@"Records"];
    [archiver finishEncoding];
    [data writeToFile:[self dataFilePath] atomically:YES];

}
// create to the file;

- (void)loadRecords{
    NSString *path = [self dataFilePath];
    if([[NSFileManager defaultManager] fileExistsAtPath:path]){
        NSData *data = [[NSData alloc] initWithContentsOfFile:path];
        NSKeyedUnarchiver *unarchiver = [[NSKeyedUnarchiver alloc] initForReadingWithData:data];
        self.allRecords = [unarchiver decodeObjectForKey:@"Records"];
        [unarchiver finishDecoding];

    }else{
        self.allRecords = [[NSMutableArray alloc] initWithCapacity:10];
    }

}
// load from the file.
```
Meanwhile you define the encode and second method or the object:

```
@interface BSRecord : NSObject<NSCoding>
- (id)initWithCoder:(NSCoder *)aDecoder{
    if ((self = [super init])) {
        self.recordDate = [aDecoder decodeObjectForKey:@"BSDate"];
        self.recordValue = [aDecoder decodeObjectForKey:@"BSValue"];
    }
    return self;
}
// define the init method the decode the object
- (void)encodeWithCoder:(NSCoder *)aCoder{
    [aCoder encodeObject:self.recordDate forKey:@"BSDate"];
    [aCoder encodeObject:self.recordValue forKey:@"BSValue"];

}

// define the encode method to encode the object
```
!!! implement to the view controller ’s initWithCoder method to let the view controller to get the data from file:

```
- (id)initWithCoder:(NSCoder *)aDecoder{

    if ((self=[super initWithCoder:aDecoder])) {
        [self loadRecords];
    }
    return self;

}
```
initWithCoder, for view controllers that are automatically loaded from a storyboard

For refer: p124 in the IOS_apprentice_2_checklit.

Create an Alert Action in IOS:

```
  -(void)showAlertMessage:(NSString *) myMessage{
      UIAlertController *signInActionController = [UIAlertController alertControllerWithTitle:@"" message:myMessage preferredStyle:UIAlertControllerStyleAlert];
       Create an AlertViewController first

      UIAlertAction *canle = [UIAlertAction actionWithTitle:@"okay" style:UIAlertActionStyleDefault handler:nil];

       //Create an Action then
      [signInActionController addAction:canle];
      //Add the Action
      [self presentViewController:signInActionController animated:YES completion:nil];
       //present that action in the view.
  }

  Make the Activity Share
      UIActivityViewController *activityVC = [[UIActivityViewController alloc] initWithActivityItems:@[self.activityTextField.text] applicationActivities:nil];
      [self presentViewController:activityVC animated:YES completion:nil];
  // based on the acitivityitems type to decide which app to share, if I change to self.actitivyTextField. it is an object so no app can accept that share!
  //根据items的类型来确定能share给的app
```
