---
layout: post
title: "图表君聊docker-基本概念"
date: "2016-10-01 23:25"
tags:
  - docker
---

# 图表君聊Dokcer-Container
上篇文章我们介绍了Docker三大概念中的Image，这篇我们来介绍Container，上篇文章中我们了解到了Image是运行在Container中的，实际上在容器里跑的指令都是在Container中run的。

## 启动容器
启动容器的方法一般有两种：

* 基于一个Image重新启动一个新的容器
* 启动一个现在已经是在Stopped状态下的容器。
来试着运行下边的这个命令：

``` 
	docker run ubuntu /bin/echo 'Hello Docker'
	Hello Docker
```
有没有感觉和本地执行 echo ‘Hello Docker’ 的速度没什么差别？但是其实人家是在一个Container里运行的啊。想想之前用VM的情况，启动一个instance得2分钟，***所要执行的Job只有几秒而已***。如果同样的Job放到docker里跑，那会快多了，太爽快了。

所以一般用户在使用容器的时候都是随时新建和删除容器的。

上边那个例子是我们用docker 运行了一个输出语句，下面我们来看这样一个例子：

```
	docker run -t -i ubuntu /bin/bash
	root@42099bcd8196:/# ls
	bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```
这样进入了一个docker container 并运行ls命令。-t让docker分配一个伪终端绑定到容器的标准输入上， -i让容器的标准输入一直打开。

## 容器的启动过程
那么一个容器的启动到底经历那些过程呢：

* 检查images如果本地不存在就是从远程仓库下载。
* 利用镜像创建并启动一个容器
* 分配一个文件系统，并在只读的镜像层外层挂接一个写读写层。
* 从宿主主机配置的网桥接口桥接一个虚拟接口到容器中
* 分配一个ip地址给容器
* 运行用户给定的应用程序
* 运行完毕，容器被终止。

容器的启动过程，对于理解容器至关重要，需要深入的理解。

使用```docker ps -al```查看所有的历史，使用```docker start```可以启动一个已经终止的容器。

## 后台运行

添加 ```-d```参数可以后台运行docker container

```
	docker run -d ubuntu  /bin/bash -c "while true; do echo hello docker; sleep 2; done"
```

这样我们就看不到容器的输出信息了，可以通过docker logs来查看

```
docker logs [container ID or NAME]
```
like this 

```
docker logs 133e58dbdc78
hello docker
hello docker
hello docker
hello docker
...
```

## 停止容器
要停止一个容器也是相当简单的，```docker stop```就能办到了。同时，对于终止状态的容器，我们可以采用```docker start ```来启动。通过```docker restart```来重启这个容器。

```
docker stop [CONTAINER ID OR NAME]
```

## 进入容器
当我们使用 -d参数运行了一个Container的时候，有时候我们需要进入这个容器进行一些操作。例如有这样的一个情况，我们运行了一个app在一个容器里，我们想进入容器看看，这个app运行的状态，查看log。那们如何进入呢？其实有很多种方法，这里介绍两种。
第一：

```
	docker attach [CONTAINER ID OR NAME]
```	

```
$ docker run -d --name topdemo ubuntu /usr/bin/top -b
$ docker attach topdemo

top - 02:05:52 up  3:05,  0 users,  load average: 0.01, 0.02, 0.05
Tasks:   1 total,   1 running,   0 sleeping,   0 stopped,   0 zombie
Cpu(s):  0.1%us,  0.2%sy,  0.0%ni, 99.7%id,  0.0%wa,  0.0%hi,  0.0%si,  0.0%st
Mem:    373572k total,   355560k used,    18012k free,    27872k buffers
Swap:   786428k total,        0k used,   786428k free,   221740k cached

PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND
 1 root      20   0 17200 1116  912 R    0  0.3   0:00.03 top

```
这里我们使用了attach命令进入了容器，看到了top命令的输出。

回到我刚才说的那个例子，如果有个Container后台里执行的是一个webapp，如何看到logs 或者是输出呢？接下来具体说说。

```
docker run -d --name webapp -p 5000:5000 training/webapp
```
在执行了这个命令后，我们在后台run了一个images为training/webapp的Container（这是一个python的flask app）我们将本机的5000端口与Container的5000端口（flask的默认端口）进行了mapping。当我们从Browser访问的时候，我们就能访问到这个app。

当我们在本地开发的时候，我们很容易的可以从console里看到，这个app的访问的记录，同时也能方便的查看log文件。那么这些在容器里怎么进行呢？当然我们可以使用上边介绍的attach命令。

```
docker attach webapp
```
当从浏览器访问这个app的时候，通过attach我们可以看到Container里的输出。

```
docker attach webapp
172.17.0.1 - - [04/Oct/2016 04:44:23] "GET / HTTP/1.1" 200 -
172.17.0.1 - - [04/Oct/2016 04:44:24] "GET / HTTP/1.1" 200 -
```
但是问题来了，如果我们要看log文件，或者想进入Container，看看其他的文件状态，该怎么办呢？那么这时候我们就会用到 ```docker exec```了。

```
	docker exec -it webapp /bin/bash
	root@e0cac87036f0:/opt/webapp# ls
	Procfile  app.py  requirements.txt  tests.py
	root@e0cac87036f0:/opt/webapp#
```

通过docker exec我们就在这个Container里又运行了bash，这时候我们就能做其他我们想做的事情了。

那么```docker attach``` 和 ```docker exec```有什么区别呢？

__```docker attach``` 让用户可以进入Container查看输出等等操作，但是并不会另外启动一个进程!       如果你用```CTRL-c```来退出，同时这个信号会kill Container（默认情况）__

__```docker exec``` 会启动另外一个进程来进入Container，这里的操作是在这个进程下的。如果你用```CTRL-c```来退出，不会kill 原来的Container__

好了，对于Container今天就聊到这里，下片文章我们继续聊最后一个概念，Docker仓库。








	






