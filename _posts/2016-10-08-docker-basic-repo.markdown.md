---
layout: post
title: "图表君聊docker-仓库"
date: "2016-10-08 23:25"
tags:
  - docker
---

# 图表君聊docker-仓库
今天我们来继续聊docker，上篇文章我们介绍了docker里的Container，今天来继续三大概念中的最后一个仓库（Repository)。

当我做好了一个Image，我该怎么和其他人分享呢？答案很简单，我们应该把他push到一个仓库里，这样其他人也能使用我的Image了。这个仓库可以使一个私有的仓库，供一个team内部使用。也可以是一个公共的仓库，开发给所有使用。

目前docker官方维护一个公共仓库 [Docker Hhub](https://hub.docker.com/),里边有大量的image，可以满足我们的大部分需求。

当然首先你能注册一个docker hub的账号，当然由于众所周知的原因，你需要用一些科学的手段才能注册上。

## 登录
当注册好docker hub的账号以后，就可以通过 ```docker login```来登录了。login后我们可以搜索自己需要的image来使用。
like this:

```
docker search python
NAME                           DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
python                         Python is an interpreted, interactive, obj...   1285      [OK]
kaggle/python                  Docker image for Python scripts run on Kaggle   40        [OK]         
azukiapp/python                Docker image to run Python by Azuki - http...   4         [OK]         
dalenys/python                 Docker image of Python.                         4         [OK]       
vimagick/python                mini python                                     3         [OK]      
pandada8/alpine-python         An alpine based python image                    3         [OK]     
```

当选择好相应的的image后，我们就可以 pull Image到本地进行使用了。

## PUSH Image
有了docker hub，就可以讲本地的image push 到hub上这样，其他人就可以进行使用了。
首先我们先tag 一个image，然后将其push到我们的repo里。

```
docker tag image YOURNAMEHERE/image
docker push YOURNAMEHERE/image
```

```
docker tag training/webapp fmcand/pythonapp
docker push fmcand/pythonapp
The push refers to a repository [docker.io/fmcand/pythonapp]
```
ok,现在登录docker hub你就可以看到自己push的image了。


## Auto Build
之前介绍过我们build 一个docker Image可以通过dockerfile的方式来进行，但是我们还没有详细介绍dockerfile。（其实Dockerfile是下一篇文章的主题）我们可以通过Dockerhub 上的Auto Build的方式来自动的创建Image。简单说，过程是这样的：

* 在我们的代码里添加dockerfile用于描述如何build 包含我们app的docker image
* 将我们的github repor 和docker hub 进行配置链接
* 每次我们checkin 代码的时候就会自动的trigger docker hub 去build image

这部分内容后边的文章会详细的介绍，大家如果现在看不太明白可以不同太着急。

ok，那么问题来了，其实我们在国内的用户访问docker hub 和github可能有些问题，那么如何解决呢，其实国内的一些厂商也提供了类似的服务。后边的文章会详细的介绍。

## 私有仓库
当然，在现实的世界里，我们会需要搭建自己的docker repository，供团队内部使用。docker同时提供了自己搭建私有仓库的方法，我这里不做详细介绍了，大家可以google一下。如果确实需要，或者有什么问题，大家可以个我留言或者以后写另一文章专门介绍。

好了，docker的三大核心概念就介绍完毕了。下一篇我们继续dockerfile，并看些实战的例子。

------

原创文章，欢迎转发，但请标明出处。欢迎关注图表君的公众号，一起成长。在微信中搜索 “多彩数据” 或者 “Data_Visualization”


![image]({{url}}/resources/img/wechat.jpg)







