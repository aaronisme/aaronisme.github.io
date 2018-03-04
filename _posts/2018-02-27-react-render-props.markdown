---
layout: post
title:  "Render Props - New pattern in React"
date:   2018-02-27 11:15:00
tags:
    - 前端
---

# Render Props - New pattern in React
在软件开发的过程中Code Reuse和可读性一直是开发人员致力于解决的问题，在React社区中，以往出现了很多的Pattern来解决这个问题，例如Mixin，HOC等等。[Render Props](https://reactjs.org/docs/render-props.html)是React社区中里提出的另外的一种Pattern。由React Router的Co-author Michael Jackson 提出，它与HOC等有什么区别呢？又解决了什么问题呢？在这篇文章来一探究竟。

## 问题的引出
所有的Pattern或者solution都是为了解决问题而提出的，那么render props到底是为了解决什么问题呢？我们先来看一个例子。例如在我们的App里我们实现了这样一个component

```js
import React from 'react';

class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      x: 0,
      y: 0
    };
    this.handleMouseMove = this.handleMouseMove.bind(this);
  }

  handleMouseMove(event) {
    this.setState({
      x: event.clientX,
      y: event.clientY
    });
  }

  render() {
    return (
      <div style={{ height: 800 }} onMouseMove={this.handleMouseMove}>
        <p>
          The mouse is at ({this.state.x}, {this.state.y})
        </p>
      </div>
    );
  }
}    
```
[Code Sandbox](https://codesandbox.io/s/nw394jwro4)

熟悉React的同学都知道这是一个很简单的App，他可以追踪鼠标位置给到自己。但是问题来了，万一哪天你的朋友看到这个component，说这个feature很Cool，他的app里能不能用上呢？那么该怎么解决这个问题呢？

## Code Repeat
首先第一种想法很直接，可以把这个component做为一个父Component，另外一个Component作为一个子Component。来看代码。

```js
import React from 'react';

const Dog = ({mouse}) => (
    <div>{mouse.x}, {mouse.y}</div>
)

class MouseDog extends React.Component {
    constructor(props) {
    super(props);
    this.state = {
      x: 0,
      y: 0
    };
    this.handleMouseMove = this.handleMouseMove.bind(this);
  }

  handleMouseMove(event) {
    this.setState({
      x: event.clientX,
      y: event.clientY
    });
  }

  render() {
    return (
      <div style={{ height: 800 }} onMouseMove={this.handleMouseMove}>
        <p>
          The mouse is at ({this.state.x}, {this.state.y})
          <Dog mouse={mouse} />
        </p>
      </div>
    );
  }
}
```
现在我们有了两个Component, Dog 和 DogWithMouse,其实Dog作为DogWithMouse的子Component，那么DogWithMouse Component就能利用鼠标位置。好像这样是把问题解决了，但是如果我们有另外一个Component也想要trace mouse这个feature怎么办呢？难道再重新这样写一个Component吗？显然这种方式是不可取的。

## HOC
如果熟悉react的同学，可能会想到，这很简单呀，我们写一个High order Component（HOC）就行了，的确HOC可以解决这个问题，我们来看看HOC的代码是怎么样的。

```js

import React from 'react';

const withMouse = (Component) => {
  return class extends React.Component {
    constructor(props) {
      super(props);
      this.state = {
        x: 0,
        y: 0
      };
      this.handleMouseMove = this.handleMouseMove.bind(this);
    }

    handleMouseMove(event) {
      this.setState({
        x: event.clientX,
        y: event.clientY
      });
    }

    render() {
      return (
        <div style={{ height: 800 }} onMouseMove={this.handleMouseMove}>
          <Component mouse={this.state} {...this.props} />
        </div>
      );
    }
  }
}
const Dog = ({ mouse }) => <div>{mouse.x}, {mouse.y}</div>

const EnhancedDog = withMouse(Dog)

```

在上面的代码里，我们创建了一个withMouse的HOC, 这样就把这部分逻辑抽象了出来。其他的Component想要使用我们mouse tracking的feature。只要将Component传入HOC就能得到一个enhance过的Component。pretty cool， right？如果对于HOC不太了解，可以参考react 的官方文档进行了解。 [HOC](https://reactjs.org/docs/higher-order-components.html)

HOC似乎已经完美的解决了问题，但是这种方式有没有什么缺点呢？似乎是有下面的一些缺点。
1. 间接的props传入。
让我们重新看一看Dog Component，他的props传入了mouse，但是在使用这个Component的时候，并没有地方传入了mouse。这是因为mouse是在HOC里传入的。你大概会纳闷这个Mouse是从那来的。如果维护一个大项目的时候，有时候你就会发现这种间接的props传入，是有多么坑了。

2. 命名冲突的问题。
用过react的同学不知道有没有经历过大量使用HOC的场景，笔者是见过多层HOC嵌套使用的项目代码。就像这样： `const NewComponent = withA(withB(withC(...(Component))))` 
  如果你用过这样的多层嵌套的HOC，那么就可能会发生命名冲突的问题。来看代码：

```js

const withB = (Component) => {
  return class extends React.Component {
    render() {
      return (
        <div style={{ height: 800 }} >
          <Component mouse={'hehe'} {...this.props} />
        </div>
      );
    }
  }
}

const EnhancedDog = withB(withMouse(Dog))

```
在这里定义另外的一个HOC withB。它也给props上传了一个同样的mouse， 如果再用它来wrap `withMouse(Dog)` 时，你会发现mouse tracking的feature就不work，但是React不会有任何提示，这是命名冲突的问题。如果你有一个七八层的wrapp到一起的Component，如果出现这样的命名冲突的问题，那么就有的debug了。

## Render props
好了，既然HOC有这样的问题，那有没有什么方式既可以做到code reuse，同时也可以避免HOC的这些问题呢？答案就是Render Props。Render Props是React Traning团队提出的一种新的React中组织Component的方式，同时也是十分的简单，我们来看上边的例子中，如果用render props如何解决code reuse的问题。

```js
import React from 'react';
import { render } from "react-dom";

class Mouse extends React.Component {
    constructor(props) {
    super(props);
    this.state = {
      x: 0,
      y: 0
    };
    this.handleMouseMove = this.handleMouseMove.bind(this);
  }

  handleMouseMove(event) {
    this.setState({
      x: event.clientX,
      y: event.clientY
    });
  }

  render() {
    return (
      <div style={{ height: 800 }} onMouseMove={this.handleMouseMove}>
        {this.props.render(this.state)}
      </div>
    );
  }
}

const DogMouse = () => (
  <Mouse render={({x, y}) => (<div>this mouose is ({x}, {y})</div>})>
)

render(<DogMouse/>, document.getElementById('app'))
```
[CodeSandBox](https://codesandbox.io/s/vyjqyyl1o3)

上边就是render props的一个简单例子，我们看完会发现特别的简单，没什么特别的。Mouse就是一个普通的Component，唯一不同的是这个Component上定义了一个叫render的prop，是一个函数。在Mouse中会将state作为参数调用这个函数。而创建的DogMouse同样也是一个普通的Component，其中定义render的方法，用于定义要render什么Component。我们看看render props和HOC相比解决了什么问题：

1. code reuse。和HOC一样的，render props同样解决了code reuse的问题。把mouse tracking的feature抽象成为一个Mouse的Component，如果有另外一个Component像复用这个feature的时候，简单的定义另外一个Component定义相应的render props就好了。

2. 间接的props传入，render props 没有这个问题，可以清楚的看到props(x, y)是从什么地方出入的.

3. 命名冲突。利用render props是没有Component wrapp的，所以除非定义Component时候自己命名重复，否则不会有命名冲突的问题。

从上面的例子上看，render props可以再大多数情况下替代HOC。react的官方文档上，目前也正式的介绍了[render props](https://reactjs.org/docs/render-props.html),下次让你想用HOC的时候，来试一试Render props把。