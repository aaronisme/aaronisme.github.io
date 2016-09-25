---
layout: post
title: "图表君聊docker-基本概念"
date: "2016-09-26 09:25"
tags:
  - docker
---

上篇文章我们介绍了docker的历史由来带来的一些好处，从这篇文章开始，我们开始正式的进入Docker的世界，首先给大家介绍一下Docker一些基本概念。

### Docker的基本概念
Docker的基本概念并不是很多，就是三个：

* 镜像（image）
* 容器（Container）
* 仓库（Repository）

深入理解这三个概念，对于docker的理解会有很大的帮助。

1. 什么是image呢，简单来说image就是一个镜像，一个系统的snapshot,可以类比于一个vm的image，或者如果你用过AWS，类似于一个AMI文件。

2. 什么是Container，Container是简易版的Linux环境，可以类比的与一个Virtual Machine 或者 一个EC2的instance。
3. 那个image和Container什么关系呢？一个docker Container 需要加载一个image然后执行。image是run在Container里的。
4. 什么是repository呢？repository是一个image仓库，可以将打好的Docker image push这个仓库中与他人分享。

![image]({{url}}/resources/img/image-run-container.PNG)

相信上篇文章后大家已经把docker安装好了吧。下边我们就来一步步的介绍这个三个概念。

### Docker Image
运行```docker pull```命令可以从仓库中获取镜像。

```
	docker pull ubuntu:16.04
```
当运行这条命令的时候，实际上是从docker hub 上来请求标记为16.04 的Ubuntu image，当然由于众所周知的原因，pull的速度会很慢。所以我们可以选择从国内的一些repository来pull images。例如这样：

```
	docker pull daocloud.io/ubuntu:14.04
```

如何看到我们本地已经pull下来的images呢？使用```docker images```可以列出本地已有的镜像。

```
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
python              3.5-alpine          4f778158195b        5 days ago          87.58 MB
python              3.5.2-alpine        4f778158195b        5 days ago          87.58 MB
python              2.7-alpine          8b2171e895fd        3 weeks ago         71.97 MB

```
我们可以看到他是来自哪个仓库的，image的标价，全局唯一的ID，创建的时间 和镜像的大小。同样如果我们想查看哪个仓库的images 可以这样：

```
	docker images ubuntu
```
images下载好了，我们怎么运行这个image呢？easy

```
	docker run -t -i ubuntu /bin/bash
	root@fc8e5743f790:/#
```
这样我们就使用这个image创建了一个Container 并运行bash应用。ps.上边的 -t 让docker分配一个伪终端并绑定到容器的标准输入上，-i 让容器的标准输入保持打开。

下边的一个问题是如何创建一个image呢，有两种方法，一种是我们基于现有的image，例如这样：

```
	docker run -i -t ubuntu /bin/bash
	root@c5c7fa33b061:/# apt-get update && apt-get install -y curl
	...
```
我们创建了一个docker container 并在其中装上curl，这是注意我们得记下他的ID。

```
	docker commit c5c7fa33b061 ubuntu-have-curl
	docker history ubuntu-have-curl
```
我们使用了docke commit 生成了一个new image ‘Ubuntu-have-curl’，并用docker history看看这个image的历史。
下来我们用这个image来curl一下。

```
docker run ubuntu-have-curl curl https://www.baidu.com
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  2443  100  2443    0     0  12234      0 --:--:-- --:--:-- --:--:-- 12215

```
easy? Yes.这样我们就build一个带curl的Ubuntu image，你可以把他push 到 docker hub上，让更多人使用了。

使用docker commit 可以对于一个镜像做些简单的扩展，但不方便分享和他人的利用。另外一种方式是使用dockerfile，这是更加通用的方法，这里暂不详解，后边会专门的介绍dockerfile。

我们看了pull，build，run一个image，下来看看如何删除吧。命令很简单 ```docker rmi```

```
	docker rmi ubuntu:14.04
```
当我们使用了一段时间以后，我们运行```docker images```会发现有很多没有tag的images，大量占据着磁盘空间，那么势必就要清理下了。
运行下边这条命令，我们就可以清理下了。

```
$ sudo docker rmi $(docker images -q -f "dangling=true")

```








