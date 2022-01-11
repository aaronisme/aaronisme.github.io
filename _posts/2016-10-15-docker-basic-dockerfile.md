---
layout: post
title: "图表君聊docker-Dockerfile"
date: "2016-10-15 23:25"
categories: Learning
tags:
  - docker
---

# 图表君聊docker-Dockerfile
前边几篇文章给大家介绍了docker的三大基本概念。可能大家觉得概念的东西比较生涩，有没有更多实战的例子呢？好了，从这篇文章开始，我会给大家介绍更多实际的例子来帮助大家，那么就从dockerfile开始吧。

在介绍docker image的时候，我给大家介绍了build image的两种方法，但是留了一个坑，就是DockerFile，那今天图表君就来把这个坑填了。

## 什么是Dockerfile
Dockerfile实际上是由一行行命令组成的,让用户可以方便的创建自定义镜像。下边就是一个Dockerfile的例子

```
FROM python:2.7
MAINTAINER Aaron Chen "mail@aaronchen.cn"
COPY . /app
WORKDIR /app
RUN pip install -r requirements.txt
EXPOSE 5000
ENTRYPOINT ["python"]
CMD ["app.py"]
```
这个例子是启动一个python flask app的Dockerfile（flask是python的一个轻量的web框架）。下边我就来介绍下dockerfile里的指令。

通常来说，Dockerfile的指令分为四类，基础镜像信息，维护者信息，镜像操作指令以及容器启动时执行的指令。常用的指令有如下：

### FROM
用于指定基础的images
 
格式为 ```FROM <image> or FORM <image>:<tag>```

一个Dockerfile里的第一条指令必须为FORM指令。

### MAINTAINER
格式为 ```MAINTAINER <name>``` 用于指定维护者信息。

### RUN
格式为 ```RUN <command>``` 
RUN命令将在基础镜像上执行相应的指令，并提交为新的镜像。

### CMD
启动Docker时运行的命令，一个dockerfile只有一个CMD起效，但是当用户在docker run 时提供了运行的命令时，CMD命令就会被覆盖。

推荐的格式为为 ```CMD["executable","param1","param2"]```

另外一种格式为 ```CMD["param1","param2"]```,配合ENTRYPOINT同时使用，其中的两个参数会提供给ENTRYPOINT。

### ENTRYPOINT
配置容器启动后执行的命令,并且不可被 docker run 提供的参数覆盖。每个 Dockerfile 中只能有一个 ENTRYPOINT ,当指定多个时,只有最后一个起 效。

### COPY
复制本地主机的```<src>```(Dockerfile所在目录的相对路径)到容器里```<dest>```

### WORKDIR
为后续的 RUN 、 CMD 、 ENTRYPOINT 指令配置工作目录。
格式为 ```WORKDIR /workdirPath```

上边就是一些基础的命令，还有其他一些命令，图表君就不一一介绍了，大家有兴趣了可以去docker官网上自己查询。
我们再来看上边的Dockerfile的例子，现在应该能看懂了把。它定义就是：

1. 从dockerhub上pull下python 2.7的基础镜像。
2. 维护者的信息是图表君
3. copy当前目录到容器中的 /app目录下
4. 指定工作路径为/app
5. 安装依赖
6. 暴露5000端口
7. 启动app

## 创建镜像
编写好Dockerfile后，就可以使用docker build来build images了。其格式为：
```docker build 选项 [路径]```，通常我们可以使用.dockerignore来定义docker build images的时候忽略的文件，通常项目的依赖我们一般会忽略（例如 npm install后的 node_modules）。我们可以使用 ```docker build . -t pythonflasksample```来build image. 然后我们就可以使用docker run 来运行容器。

## 又一个栗子
上边是一个python app的栗子，我们再来看一个前端的栗子。

```
├── .dockerignore
├── .gitignore
├── Dockerfile
├── README.md
├── app
│   ├── directives
│   ├── index.html
│   └── index.js
├── dist
│   ├── bundle.js
│   └── index.html
├── node_modules
├── karma.conf.js
├── package.jsonimage
└── webpack.config.js
```
这是一个Angular的前端project的项目目录。我们用npm来管理和安装依赖的包，用webpack来构建项目。下来用Dockerfile来定义一个docker image，将其容器化。

```
	FROM node:4.6
 	MAINTAINER Aaron Chen<mail@aaronchen.cn>
 
 	RUN mkdir /app
 	WORKDIR /app
 	COPY . /app
 
 	RUN npm install
 	
 	EXPOSE 8080
```

这里Dockerfile的定义，很简单，下载基础镜像node 4.6，安装依赖，暴露接口。

下来我们用```docker build . -t webpackdemo```来build这个image，当image构建好后，简单的执行```docker run -it -p 3456:8080 webapackdemo2 npm run start```，好了一个前端的project就启动起来了。相当的轻松，让你访问```http://localhost:3456/```的时候就可以访问了。

这里是这个project的github repo，大家可以来一看究竟。
[dockerSample](https://github.com/aaronisme/dockerSample)

## 解决了什么问题呢？
如果你是个前端人员，看到这里的时候一定会觉得，这到底解决了什么问题呢？我本地启动也没有什么问题啊。的确图表君刚开始也有这样的疑问，但经过思考，我认为解决了这几个问题：

1. 让开发的环境能更简单的搭建了。经常会有这样的场景，在别人机器上正常的开发环境，在自己的机器上为什么就是不行呢？研究了半天，发现人家用的是node 6.5，自己用的是node 0.11。在使用了docker以后就解决了这样的问题，只要pull下image就OK了，所有的版本和依赖都固定下来了。
2. 现在业绩最流行的构架方式当属于微服务了，一个系统的正常启动依赖于多个服务，有可能对于其中的某些服务我们并不熟悉，那么如果将服务容器化了以后，就能有效的解决这样的问题了。另外对于团队里的其他非技术人员使得他们也能容易的在本地搭建起环境来。

**其实，上边这个前端项目中的dockerfile有一个很大的问题，图表君给大家留个问题。如果大家看出来了，欢迎给我留言**

好了，Dockerfile就介绍这么多，我们下期再见。

------

原创文章，欢迎转发，但请标明出处。欢迎关注图表君的公众号，一起成长。在微信中搜索 “多彩数据” 或者 “Data_Visualization”


![image]({{url}}/resources/img/wechat.jpg)







