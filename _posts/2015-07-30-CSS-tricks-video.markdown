---
layout: post
title:  "CSStricks Time-如何让你的video响应式"
date:   2015-07-30 16:15:00
tags:
    - 前端开发
---
做前端的同学不知道对于CSS感觉怎么样，反正我的感觉是挺酸爽的。尤其是mobile first后，一个页面要适应多种客户端。虽然已经有想bootstrap.fundation之类的东西可以帮助我们，但是在一些时候要做到响应式还是挺麻烦的事情，有时候还有一些tricks的东西。最近在玩一些东西的时候，发现了一些，记录在这里。

当我们在web page里要嵌入一个video的时候，如何做到让他能做到响应式呢，当页面的窗口大小变化的时候，video的大小也会发生相应的变化。如何做到呢？分以下的几种情况：
当使用video tag的时候最简单的方法是使用以下的CSS:
{% highlight CSS%}
video{
	max-width:100%;
	height:auto;
}
{% endhighlight %}

如此的简单暴力，直接设置了max-width，以及height就ok了。当时这种方式有个问题，就是当使用iframe或者object tag的时候，这样的方法就是不行了。因为当嵌入很多视频网站的video的时候，上述方法就不行了。那如何解决这样的问题呢？
其实也是很简单的。就是将iframe 或者 object tag 用一个div标签包起来。设置这个div的padding-bottom为50% - 60%，postion 为relative。然后将iframe or tag 标签的width 和height标签设为100%，postion设为 absolute。这就迫使iframe or object 标签占据div的全尺寸。上代码：
CSS:
{% highlight CSS%}
.video-container{
	postion:relative;
	padding-top:30px;
	padding-bottom:56%;
	height:0;
	overflow:hidden;
}
.vide-container iframe,
.vide-container object,
.vide-container embeded{
	postion:absolute;
	top:0;
	left:0;
	width:100%;
	height:100%;
}
{% endhighlight %}

html:
{% highlight html%}
	<div class="video-container">
		<iframe src="http://player.vimeo.com/video/6284199?title=0&byline=0&portrait=0" width="800" height="450" frameborder="0"></iframe>			
		</iframe>
	</div>
{% endhighlight %}
我是看到这篇文章的发现这些tricks的。大家可以参考[一下](http://avexdesigns.com/responsive-youtube-embed/)。
