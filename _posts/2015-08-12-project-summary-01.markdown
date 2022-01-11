---
layout: post
title:  "Ionic 项目总结与分享"
date:   2015-08-12 22:00:00
categories: Building
tags:
    - 前端
    - Summary
---
IONIC 是较为红火的mobile app 开发框架，所以花了点时间研究了一下，一下就是对于具体实施的一些项目中的总结和分享。

app的设计思想

与web不同的是，在app中的设计的思想是要注意数据的cache的，从本质上来讲，app就是原来的desktop上的软件，但是在目前的情况下，所有的app基本上都是有网络请求的，实际上的产品结构师CS的。所以app是client端，那么有client端就一定有数据的本地存储，在app上一般是使用sqlite的方式去进行存储的。所以下边就简单说下如何在ionic中引入sqlite的plugin。
[参考文章](https://blog.nraboy.com/2014/11/use-sqlite-instead-local-storage-ionic-framework/)
首先引入cordova 的plugin，然后引入ngcordova，将ngcordova注入到ionic的angular里，在初始化app的时候create table，然后可以使用cordova的其他方法来进行数据的处理。this part will be described later

videogular的使用
在github上900多star的videogular被引入到项目中来，还是在继续的看源码，写到不错。

fullscreen的支持
使用了cordova的fullscreen的插件进行使用。在为让用户在点击全屏的时候，自动横屏全屏，修改了videogular的源码，但是目前ios上有些问题，andriod上可以。ios不能inline play，在点击播放的时候会调用native player。this issue should be more investigate。

share to wechat
使用了github上 xu.li.cordova.wechat的plugin。了解到了一些基本的东西，在ios apple上的app id 就是 bundle id 可以唯一标示一个app，但在android上就需要包名，以及签名来标示，在ionic上包名在config.xml中
{% highlight XML%}
<widget id="com.ionicframework.badyapp960848" version="0.0.1" xmlns="http://www.w3.org/ns/widgets" xmlns:cdv="http://cordova.apache.org/ns/1.0">
{% endhighlight %}
[参考文档](http://www.ionicframework.com/docs/guide/publishing.html)
第一步 build --release
第二步 生成 keystore
第三步 签名
第四部 优化 apk

这就是第一周的项目总结。后边一周总结一下。
