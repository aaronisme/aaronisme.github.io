---
layout: post
title:  "手把手-如何在Android上调试Rust Code"
date:   2022-02-08 01:15:00
published: false
categories: Thinking
tags:
    - Rust
    - 手把手
---

Rust是一门最近发展很快的语言, 最近一段时间我和我们天团队也在一直使用Rust来建构我们的业务. 我们尝试将我们核心的业务代码用Rust来重写, 然后通过使其运行在不同的平台下边.其中一个问题就是如何在不同平台上进行代码调试.

# Dinghy
通过在Rust社区进行一番查找, 发现了Dinghy这个插件发现非常的符合我们的需求, 由于我们的硬件平台是Android系统. 所以用Android系统为例, 介绍一下如何使用Dinghy这个插件在Android系统上进行代码调试的运行.