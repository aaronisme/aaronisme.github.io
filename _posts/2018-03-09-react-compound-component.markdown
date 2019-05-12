---
layout: post
title:  "React in Depth - Compound Component"
date:   2018-02-27 11:15:00
tags:
    - 前端
---

使用React也有一段时间了，前边写了一些React基本概念的文章。今天开始写React in Depth这个系列文章。在这一系列文章中，我会总结和分享下React中的开发经验，希望能和大家一起分享，共同提高。今天是第一篇Compound Component。

React本质来讲是只做View的Framework，所以Component是React核心和最基本的组成部分，使用React开发出来的application本质来讲就是把一个个组件拼接起来。因此深入理解Component，并且如何在项目上开发出一个合适Component，是一个需要深入理解的问题。

让我们从一个现实中的例子开始，下面是两个风扇。

<div style="text-align:center">
    <img src="/resources/img/fan-1.jpg" style="display:inline-block; width:250px; height:250px">
    <img src="/resources/img/fan-3.jpg" style="display:inline-block; width:250px; height:250px">
</div>

一个是我们日常生活中的风扇，它有一个按键，当按键按下的时候，风扇就转了。另外一个是arduino风扇，同样接通电源后风扇就转了。但是如果那天用户希望风扇反转，那么这两个产品能满足需求吗？对于第一个风扇我们得把它拆开，然后修改电机的接线，这样才能满足反转的需求，但是对于第二个我们只要简单的