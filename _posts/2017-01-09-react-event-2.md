---
layout: post
title: "浅析React之事件系统（二）"
date: "2017-1-09 08:01"
tags:
  - react
---
# 浅析React之事件系统（二）

上篇文章中，我们谈到了React事件系统的实现方式，和在React中使用原生事件的方法，那么这篇文章我们来继续分析下，看看React中合成事件和原生事件混用的各种情况。

## 上一个例子
在上篇文章中，我们举了个例子。为了防止大家不记得，我们来看看那个例子的代码。

```js
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
  
  handleClickImage(e){
    console.log('in this ')
    e.stopPropagation();
  }
  
  render(){
    return (
      <div className="container">
        <button onClick={this.handleClick}>Open Image</button>
          <div className="img-container" style={{ display: this.state.show ? 'block': 'none'}} onClick={this.handleClickImage}>
            <img src="http://blog.addthiscdn.com/wp-content/uploads/2014/11/addthis-react-flux-javascript-scaling.png" />
          </div>
      </div>
    )
  }
}
ReactDOM.render(<App />, document.getElementById('root'));
```

这有什么问题呢？ 问题就在于，如果我们点击image的内部依旧可以收起Image，那么这是为什么呢？这是因为我们及时点击了Image的内部，body上绑定的事件处理器依旧会执行，这样就让我们的image收起来了。那我们如果不想让image收起来改怎么做呢？

首先的想法是停止冒泡，如果我们在img-container中就停止冒泡了是不是就可以让image不消失了呢？比如这样：

```js
...
handleClickImage(e){
    e.preventDefault();
    e.stopPropagation();
  }
  
  render(){
    return (
      <div className="container">
        <button onClick={this.handleClick}>Open Image</button>
          <div className="img-container" style={{ display: this.state.show ? 'block': 'none'}} onClick={this.handleClickImage}>
            <img src="http://blog.addthiscdn.com/wp-content/uploads/2014/11/addthis-react-flux-javascript-scaling.png" />
          </div>
      </div>
    )
  }
...
```
[Open In CodePen](http://codepen.io/aaronisme/pen/jyPRPJ?editors=0010)

在这里我们定义一个handleClickImage的方法，在其中我们执行取消默认行为和停止冒泡。那是似乎效果并不是我们想要的。因为阻止React事件冒泡的行为只能用于React合成事件中，没法阻止原生事件的冒泡。同样用React.NativeEvent.stopPropagation()也是无法阻止冒泡的。

如何解决这样的问题呢？首先，尽量的避免混用合成事件和原生事件。需要注意的点是：

1. 阻止react 合成事件冒泡并不会阻止原生时间的冒泡，从上边的例子我们已经看到了，及时使用stopPropagation也是无法阻止原生时间的冒泡的。
2. 第二点需要注意的是，取消原生时间的冒泡会同时取消React Event。并且原生事件的冒泡在react event的触发和冒泡之前。同时React Event的创建和冒泡是在原生事件冒泡到最顶层的component之后的。我们来看这个例子：

```
class App extends React.Component {
  
  render(){
   return <GrandPa />;
  }
}

class GrandPa extends React.Component {
  constructor(props){
      super(props);
      this.state = {clickTime: 0};
      this.handleClick = this.handleClick.bind(this);
  }
  
 handleClick(){
   console.log('React Event grandpa is fired');
  this.setState({clickTime: new Date().getTime()})
};
  
  componentDidMount(){
    document.getElementById('grandpa').addEventListener('click',function(e){
      console.log('native Event GrandPa is fired');
    })
  }
  
  render(){
    return (
      <div id='grandpa' onClick={this.handleClick}>
        <p>GrandPa Clicked at: {this.state.clickTime}</p>
        <Dad />
      </div>
    )
  }
}

class Dad extends React.Component {
  constructor(props){
    super(props);
    this.state = {clickTime:0};
    this.handleClick=this.handleClick.bind(this);
  }
  
  componentDidMount(){
    document.getElementById('dad').addEventListener('click',function(e){
      console.log('native Event Dad is fired');
      e.stopPropagation();
    })
  }
  
  handleClick(){
    console.log('React Event Dad is fired')
    this.setState({clickTime: new Date().getTime()})
  }
  
  render(){
    return (
      <div id='dad' onClick={this.handleClick}>
       <p>Dad Clicked at: {this.state.clickTime}</p>
        <Son />
      </div>
     )
  }
}

class Son extends React.Component {
  constructor(props){
    super(props);
    this.state = {clickTime:0};
    this.handleClick=this.handleClick.bind(this);
  }
  
  handleClick(){
    console.log('React Event Son is fired');
    this.setState({clickTime: new Date().getTime()})
  }
  
  componentDidMount(){
    document.getElementById('son').addEventListener('click',function(e){
      console.log('native Event son is fired');
    })
  }
  
  render(){
    return (
      <div id="son">
       <p onClick={this.handleClick}>Son Clicked at: {this.state.clickTime} </p>
      </div>
     )
  }
}

ReactDOM.render(<App />, document.getElementById('root'));

```

[Open in CodePen](http://codepen.io/aaronisme/pen/Kaewxz?editors=0001)

在这个例子中我们有三个component，Son Dad，Grandpa。同时定义了React Event handler 和 native event handler，并在Dad的native Event handler中stopPropagation，当我们点击Son or Dad component的时候会发现，React Event handler并没有被trigger。
console里的output为：

```
"native Event son is fired"
"native Event Dad is fired"
```

这就说明native Event的停止冒泡可以阻断所有的React Event。所以即使我们是在Dad上停止冒泡的，依旧阻断了Son上的React Event。

同时如果我们把dad上的stopPropagation remove掉我们会看到如下结果：

```
"native Event son is fired"
"native Event Dad is fired"
"native Event GrandPa is fired"
"React Event Son is fired"
"React Event Dad is fired"
"React Event grandpa is fired"
```
这就说明React的合成时间是在原生事件冒泡到最顶层组件结束后才创建和冒泡的，也是符合React的原理，因为在是实现的时候React只是将一个Event listener 挂在了最顶层的组件上，其内部一套自己的机制进行事件的管理。




