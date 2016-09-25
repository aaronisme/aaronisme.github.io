---
layout: post
title: "周总结"
date: "2016-09-25 09:25"
tags:
  - Summary
---

## 周总结
这个周学到那些东西呢？

* 写代码的时候，特别是logging error的时候，如何能够更好的定位Issue这是为了帮助自己，例如在做loader的validator的时候，如果放到转换里边，并不知道现在validator的数据是那个文件的，那么这样如果id，name都为空的时候，这样就不能给一个有效的提示了。
还有在validator一个文件的时候，是发现一个错误就报出来，然后部去处理其他，还是全部validator以后把错误的field都报出来。显然第二方式是更好的方式了。

* Ruby Rspec 中的 raise error 常常要进行regex的判断，regex 是不加引号的，加了引号就成为string了. 直接 /^XXXXXXX/就OK了。
* Rails 里的Create 是不写入了，create！是写入的。
* Rails Model.Create!([]).这样一次插入多条，这是一个transcation，但有错了是一次都rollback回去的。所以批量插入的时候就有个validator的问题了，什么时候validator？
	1. 插入之前，都validator一把，把不合适的filter过，再插入。
	2. 直接试着插入，看看有没有raise error，catch住error再validator 一把，滤掉后再插入。
	
	第一种方法是可能有性能问题。如果错误的机会不大，实际上是浪费。第二种没有这个问题，但是logic上有点奇怪，但是可以考虑采用。
* Rspec Mock，or UT中的Mock，UT是为了测试一个Class的logic 所以有些引入的其他类就要mock掉，还有，例如AWS connect， 从ENV获取数据的这些操作的类，都要mock掉，不然真真就是连AWS，CI上没有环境变量怎么搞？然测试的东西与其他隔离，这是好测试的标准。

* 这周的学习主题是Docker。