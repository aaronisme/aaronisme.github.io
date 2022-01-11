---
layout: post
title:  "CSStricks Time-让video响应式原理分析"
date:   2015-08-03 10:15:00
categories: Learning
tags:
    - 前端
---
上一篇分享到了如何让video可以做的响应式，那么今天我们具体来分析一下到底是如何做到的。首先现在的目前的video都是有固定的比例的，一如4:3，一如16:9。无论我们如何缩放，我们也要保证这个比例是不会变化的。所以这是视频缩放的前提。

首先给介绍一个css tricks 那就是padding property，padding property 可以让一个box有一个固定的比例。这既是当我们将padding设为%以后那么它基于他的container的width大小计算的。有了这个trciks我们就可以用他来建一个固定比例的box了。来看代码：
{% highlight HTML%}
<div class="video-container">
	<div class="element-to-strech">
	</div>
</div>
{% endhighlight %}
{% highlight CSS%}
.video-container{
	position: relative;
	padding-bottom: 20%;
	height: 0;
}
.element-to-strech{
	position: absolute;
	top: 0;
	left: 0;
	width: 100%;
	height: 100%;
	background-color: green;
}
{% endhighlight %}
来让看下这个css片段。在.video-container中设置了，postion：relative目的是为子元素定位提供一个位置定位。padding-bottom设置为20%，这样就创建了一个比例为5:1的box。
在其子元素中设为postion：absolute，这样就可以让子元素可以突破container的height的限制。那就这个元素就可以被定位在父元素的padding区域里了。
后边的就好理解了，top，left定位位置，width，height让与container同宽高。这样我们就创建好了一个固定5:1可缩放的container。

下面让我们看下一个真实的例子：
在上边的说明的例子我们container与body同宽的，那么在实际应用的时候，我们经常是有一个区域来展示video的所以我们有个warpper将上述的container包裹起来。
我们看如下的例子：
{% highlight HTML%}
<div class="warpper">
	<div class="video-container">
		<iframe src="" frameborder="0"></iframe>
	</div>
</div>
{% endhighlight %}
{% highlight CSS%}
.warpper{
	width:50%;
}
.video-container{
	position: relative;
	padding-bottom: 56.25%;
	height: 0;
}
.vidoe-container iframe{
	position: absolute;
	top: 0;
	left: 0;
	width: 100%;
	height: 100%;
	background-color: green;
}
{% endhighlight %}
与上面的例子相比，我们加了一warpper控制video的width。其他的都是类似的。pading-bottom的为56.25%这是将比例设定在16:9。如果我们将比例控制在4:3则，padding-bottom为75%。

以上就是对于响应式video的分析，css中并没有考虑IE5,6,7等奇葩浏览器。这里我看到的更全的连接，给大家参考。
[链接](http://www.cssmojo.com/creating-intrinsic-ratios-for-video/)
