---
layout: post
title:  "IOS项目与学习周记"
date:   2015-12-28 22:00:00
tags:
    - IOS
    - Summary
---
### IOS-Summary-20151228
1. add new controller in the tab application
- create an tab application
- add the view controller
- control - drag to the new view controller
- choose view controllers
these are the steps! more you can access this post:
[the post link](http://codewithchris.com/ios-tab-bar-app/)

2. change the cell format to subtitle and can change the image for the cell
- add the image to the assets.xcassets
- change the image name
- in code you can access the image using it:
```objectivec
	image = [UIImage imageNamed:@"pass"];
```

3. unwind segue
	- define the segue in the destionation viewController
	- in the operation page you can drag the button to the exit in the top and choose the defined segue (**but only when using size class are chosen**)
	- in the 出发的viewController 可以在 Document outline中看到出发的viewController里定义的segue。可以加入identifier的Name，在code中就可以调用这个segue。
	```
			[self 	performSegueWithIdentifier:@"unwindToSugerView" sender:self];
	```

4. 使用tableview 来做页面layout 定义了几个section 但是想把空白的地方显示为空白，不要显示table cell。这里有个tricks：
5.tableview的头里有空白，如何消除这些空白呢，在其attribute inspector 里 uncheck [the post link](http://stackoverflow.com/questions/27827315/empty-white-space-above-uitableview-inside-a-uiview)
