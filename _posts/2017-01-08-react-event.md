---
layout: post
title: "浅析React之事件系统（一）"
date: "2016-10-23 08:01"
tags:
  - React
---
# 浅析React之事件系统（一）

大家周末好，2016年的最后几篇文章开始写到了React的一些东西，那么最近就来一些图表君对于React的简单总结和理解，那么今天就开始第一篇，说一说React的事件系统。

## 总览
简单来说React实现了一个SyntheticEvent层，所有定义的事件处理器都可以接受到一个SyntheticEvent对象的实例，他是一个跨浏览器的对于原生事件的包装，和原生事件一样有同样的接口，包括stopPropagation()和preventDefault()。

## 合成事件的使用方式
在React中不会把所有的事件处理器绑定到相应的真实的DOM节点上，而是使用一个统一的事件监听器，把所有的事件绑定在最外层。当事件发生的时候，首先被这个统一的事件监听器处理，随后找到真正的事件处理函数进行调用，这样是为了提高效率，这是因为在UI系统中，事件处理器越多，那么占据的内存就越大，React的做法是将其简化为一个，这样就大大提高了效率。在之前开发者需要为了优化性能需要自己来优化自己的事件处理器的代码，现在React帮助你完成了这些工作。

### 合成事件的绑定方式
说了这么许多理论上的知识，我们来看看合成事件是怎么使用的。

1. bind方法。

	我们来直接看代码
	
	```
		import React, {Component} from 'react';
		
		class EventApp extends Component {
		
			handleClick(e,args){
				console.log('this is the react event',e)
				console.log('this is the args', args)
			}
		
		
			render(){
				return <button onClick={this.handleClick.bind(this,'test')}>Test</button>
			}
		
		}
	
	```

2. 构造器内声明
	
	再来看代码
	
	```
		import React, {Component} from 'react';
		
		class EventApp extends Component {
		
			constructor(props){
				super(props);
				
				this.handleClick = this.handleClick.bind(this);
			}
		
			handleClick(e){
				console.log('this is the react event',e)
			}
		
		
			render(){
				return <button onClick={this.handleClick}>Test</button>
			}
		
		}

	```
	使用构造器内声明的方法，仅仅要绑定一次而不需要每次使用的时候都绑定一次。

3. 箭头函数

	```

	class ButtonApp extends React.Component {

  		handleClick (e){
    		console.log(e.target.value)
  		}
 
 	 	render(){
    		return <button onClick={(e) => this.handleClick(e)}>Test</button>;
  		}
	}
	
	```

从上边的使用方式我们可以看出React来使用合成事假还是很简单的，但是现实的世界总是更加的复杂的。那么在React中我们可以使用原始事件吗？当然是可以的。

## 使用原生事件
在React中我们也可以使用原生事件，那么如何进行绑定呢，因为React提供了ComponentDidMount这样的API让我们可以调用，那么要使用原生事件我们就可以在DidMount后进行绑定。例如上边的那个例子中如果我们想把click事件绑定在原生button上该怎么做呢？我们来看代码：

```
class ButtonApp extends React.Component {
	
	componentDidMount(){
		this.refs.button.addEventListener('click'  e => {
			console.log(e);
		})
	}
	
	componentWillUnmount(){
		this.refs.button.removeEventListener('click')
	}

	render(){
		return <button ref="button">Test</button>
	}

}

```
在这里例子中我们使用原生事件的方法绑到了button上，注意一点的是在DidMount上add了这个listener在willUnmont上remove这个listener。一定要手动的记住移除，不然可能会出现内存泄漏问题。如果我们使用React合成事件，这些事React已经帮你做好了。但是现实的情况下我们有一些场景是不得不用到原生的事件的那么该怎么做呢？

我们来看下边的一个例子。例如我们要实现这样的一个功能，在页面上有个button，当点击它会出现一个图片。当点击页面的其他部分的时候，这个图片会自动的消失，那么这样的需求我们就不得不使用原生的事件了。话不多说我们来看代码实现。

```
import React from 'react';

class App extends React.Component {

  constructor(props){
    super(props);
    
    this.state = {
      show: false
    }
    
    this.handleClick = this.handleClick.bind(this)
    this.handleClickImage = this.handleClickImage.bind(this);
  }
  
  handleClick(){
   this.setState({
     show: true
   })
  }
  
  componentDidMount(){
    document.body.addEventListener('click', e=> {
      this.setState({
        show: false
      })
    })
  }
  
  componentWillUnmount(){
    document.body.removeEventListener('click');
  }
    
  render(){
    return (
      <div className="container">
        <button onClick={this.handleClick}>Open Image</button>
          <div className="img-container" style={{ display: this.state.show ? 'block': 'none'}} >
            <img src="http://blog.addthiscdn.com/wp-content/uploads/2014/11/addthis-react-flux-javascript-scaling.png" />
          </div>
      </div>
    )
  }
}
ReactDOM.render(<App />, document.getElementById('root'));

```
[Open In CodePen](http://codepen.io/aaronisme/pen/jyPRPJ?editors=0010)

上边一个例子中，我们实现了组件APP，他里边有一个button，它上边有一个handleClick的事件处理器，当触发时会把app的state里show制成true，这样图片就显示了出来。同时在body上使用了原生事件，当发生点击事件的时候，就会被收起，这样就简单实现了需求的功能，那是看似这样好像就没有问题的，但是这其中有个bug，到底是什么问题呢，我们下篇文章继续。看看原生事件和合成事件混用的那些事。


参考文献：
深入react技术栈






