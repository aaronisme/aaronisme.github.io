---
layout: post
title: "AWS Lambda - A BIG THING"
date: "2016-10-23 23:25"
tags:
  - Summary
---
# AWS Lambda - A BIG THING
各位周末好，今天我们先不说docker了，说说一些其他的东西，最近图表君的项目上广泛的用到了AWS Lambda。以前没觉得Lambda怎么样，最近因为项目上的需求深入的看了下，觉得AWS Lambda可能是个Big Thing。

## 什么是AWS Lambda
好了，第一个问题来了，什么是AWS Lambda。AWS Lambda是2014年底AWS推出的一个全新的服务。用户可以简单讲自己的code部署到AWS Lambda上，那么这个Lambda可以由其他的事件来trigger，这些事件的来源可以使AWS S3上的一个文件的变化，可以使Dynamo Table的一个数据的update，可是一个SNS的Topic。Lambda的出现可以使得，在使用AWS上其他云服务的时候扩展性更高。

## Lambda 能解决什么问题
好了上边的讲法有些抽象，那么Lambda到底能解决我们什么问题呢？OK，下边就是一个例子我们来看看Lambda到底能解决什么样的问题。

假定现在有这样一个场景，有一个外边的数据源，每天会定时的往S3（AWS的文件存储）放一些新的数据，然后我们自己的Service来处理这些数据，然后再做一些相应的数据处理。这样的场景相信是我们现实工作中的典型的场景。那么应该怎么来设计我们的构架呢？

------------

External DataSource --> S3  <--- Our Service 

------------
简单来说，我们可以使这样来做，我们自己写一个Service部署在一个 Instance上，这个Service不断的去监控S3当发现有数据更新的时候，将其取出来，然后做相应的处理。这样的方式是相当的自然的。那么这么做有什么问题呢？

1. 成本的问题。有可能我们数据源每天的更新次数很少（假设3次），但是更新时间是不固定的。而且每一次的处理时间只有一分钟。如果全天这个instance都是启动的，那么一天内有效的工作时间只有3分钟，其他的工作时间都是浪费的。
2. 代码的复杂度。上边的例子可能并不十分的合适，考虑下边一个场景。

-----------

SNS ---> Our Service 

------------
有个SNS的消息服务，我们的Service订阅这个消息服务，让有消息的时候，我们的Service会相应处理。那么在实际中，我们的Service可能会采用多线程的方式，并行处理这些消息来带到更快的处理效率。但是这样同时会带来代码上复杂度的提升。

那么有了Lambda，能带来什么呢？第一例子中的构建设计就变成了下边这样：

------------

External DataSource --> S3  ---> Lambda --> OtherService

------------

S3上的任何文件变化都会trigger一个Lambda，这个Lambda就可以进行相应的处理。这样让整体的构建变成了Event Trigger的。那么就能很好的解决我们一个成本问题。如果每天只有3次文件更新，那么就trigger3次Lambda处理就OK了。这样会使得成本大大降低。

再来看我们的第二个例子，使用Lambda后的构建就变成了这样：

-----------

SNS ---> Lambda --> OtherService

------------
由于Lambda 自带的Auto Scaling的特性，开发者可以基本不考虑并发的问题，当有多个message需要处理的时候，Lambda会自己来Auto Scaling来处理多个messages。


Lambda的出现让开发者能够更快的专注自己的业务场景，并且减少运维上的压力。Lambda的出现也使得Serverless的软件构建渐渐的兴起。

## Lambda的局限性
当然Lambda有一定局限性，图表君目前觉得可能影响最大的是 Maximum execution duration per request只有5分钟，这样Lambda处理一些复杂的业务场景时候就不太合适了。当然局限也不止于此，具体大家可以参考AWS的官方文档。


## 未来
Lambda的出现已经快两年了，图表君觉得这可能是又是一个能带来大改变的东西。最近Amazon又退出了一个硬件产品叫AWS IOT button，是AWS在物联网中的一个基础产品。下边这个图一看大家就明白了：

![image]({{url}}/resources/img/awsiot.png)

我们可以看到Lambda是这里的关键一环。

AWS Lambda will be a big thing.

------

原创文章，欢迎转发，但请标明出处。欢迎关注图表君的公众号，一起成长。在微信中搜索 “多彩数据” 或者 “Data_Visualization”


![image]({{url}}/resources/img/wechat.jpg)













