---
layout: post
title: "工作总结"
date: "2016-09-11 09:25"
tags:
  - Summary
---
#周总结（20160911）
## RUBY 工作
1. 如何自定义Error类型

```
RUBY ERROR
class CustomError < StanrdError
end
```

2. 测试的时候并不测试私有方法，因为测试一个Class目的是为了验证其对外的接口和定义，所以不必测试私有的方法。

3. Ruby里如何删除一个hash里某个```KeyValue pair```因为delete方法返回的是那个要删除key的value，但是原来的hash是删除过后。

```
def delete_pair_by_hash key
	hash.delete(key)
	hash
end
```




## 前端 学习工作
* 遇到的问题 在做web-UI的时候一个select的event 被两个Controller 监听到，因为两个Controller是相互嵌套的，由于JAVASCRIPT的冒泡机制，由于一个事件是一直向上冒泡的，所以被一个监听到并处理之后，并不会解决他，而是继续向上冒泡，所以就被另一个Controller处理了，这样就发生错误了。

* 遇到问题 CSS的布局问题，当在一```overflow：hidden```的container中其中的```postion:absolute```的方式不起作用。

```
	<div style="postion:relative; overflow:hidden">
		<div style="postion:absolute; top:1px right:2px"></div>
	</div>
```
并不能有效的布局，如何解决呢？Easy
前边加个一个div包起来就OK了。

```
	<div style="postion:relative">
		<div style="overflow:hidden">
			<div style="postion:absolute; top:1px right:2px"></div>
		</div>
	</div>
```
