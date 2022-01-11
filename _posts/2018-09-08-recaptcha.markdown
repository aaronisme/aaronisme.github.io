---
layout: post
title:  "当React Native 遇到了Google reCAPTCHA"
date:   2018-09-08 11:15:00
categories: Building
tags:
    - 前端
---

做客户端开发久了，总有一些烦心事来扰乱你，其中一个就是机器人注册。当然大部分App目前注册的时候都要提供短信验证码。但是这还是防不住一些专业的羊毛党，各种短信验证码平台用的飞起。那该怎么办呢？上验证码吧。验证码大家都熟悉从不可描述的12306到Google的reCAPTCHA，作用只有一个验证你是人，不是机器人。今天的主角就是Google reCAPTCHA。

## Google reCAPTCHA
Google reCAPTCHA是Google 提供的一系列好用的服务中的一个，提供完善的人机验证方法。目前有V3和V2两个版本。V3还在Beta阶段，这样我们主要介绍V2。当然同时Google reCAPTCHA也是google用来做数据标记的方法，每天成千上万的图片被人工标记，为Google的Machine Learning系统提供好的帮助。经典的双赢策略。如果还没有体会过Google reCAPTCHA这里链接[reCAPTCHA](https://developers.google.com/recaptcha/)

## 如何使用Google reCAPTCHA
Google reCAPTCHA的使用十分简单，文档中描述的清楚。下边简单的介绍一些。最简单的方法就是自动的Render Google reCAPTCHA Wideget

```html
<html>
  <head>
    <title>reCAPTCHA demo: Simple page</title>
     <script src="https://www.google.com/recaptcha/api.js" async defer></script>
  </head>
  <body>
    <form action="?" method="POST">
      <div class="g-recaptcha" data-sitekey="your_site_key" data-callback="yourCallbackFunction"></div>
      <br/>
      <input type="submit" value="Submit">
    </form>
  </body>
</html>
```
上面是个简单的html就实现了如何使用Google reCAPTCHA. 具体来说就是加载了google reCAPTCHA的JavaScript,然后定义一个class name是g-recaptcha div，这样以后reCAPTCHA的widget就会Render到它下边。当然你要在google reCAPTCHA上申请一个相应的site_key。so easy。好了当你用浏览器打开这个html的时候就可以看到Google reCAPTCHA widget被render出来了。同时定义了CallBallFunction，当验证成功时候，Google reCAPTCHA会调用这个callback，把取得的token告诉Application，那么Application就可以去进行验证了。

好了，Google reCAPTCHA如此好用的服务，在移动端可不可以使用呢？当然Google reCAPTCHA提供Android的API。但是如果我们Application是用React Native来写，是不是就不能使用了呢？当然我们有办法让它可以使用。

在React Native中是有Webview组件的，同时WebView组件和React Native之间可以通过postMessage来进行数据通信。那么已然这样，就可以通过WebView来加载一个HTML来Render Google reCAPTCHA Widget。同时通过PostMessage将 Google reCAPTCHA 返回的token，送给React Native。好了从原理上来讲是可以的，那么如何实现呢？还是看代码吧。

{% raw %}
```js
  import { WebView } from 'react-native';

  const generateTheWebViewContent = siteKey => {
    const htmlMarkup =
      '<!DOCTYPE html><html><head>' +
      '<script src="https://recaptcha.google.cn/recaptcha/api.js"></script>' +
      'var onDataCallback = function(response) { console.log(response); window.postMessage(response);  }; ' +
      '</head><body>' +
      '<div style="text-align: center"><div class="g-recaptcha" style="display: inline-block"' +
      'data-sitekey="' +
      siteKey + '" data-callback="onDataCallback" ';
    return htmlMarkup;
  };

  const RNReCaptcha = ({ onMessage, siteKey, style, url }) => (
    <WebView
      originWhitelist={['*']}
      mixedContentMode={'always'}
      onMessage={onMessage}
      javaScriptEnabled
      injectedJavaScript={patchPostMessageJsCode}
      automaticallyAdjustContentInsets
      style={[{ backgroundColor: 'transparent', width: '100%' }, style]}
      source={{
        html: generateTheWebViewContent(siteKey),
        baseUrl: `${url}`,
      }}
    />
  );
{% endraw %}
```

上边这段代码就是一个最简单的实现，定义了一个`RNReCaptcha`的Component，其实就是一个WebView，在source里我们直接给出一段html，其实就是上边那个例子的html，这样一来render这个html就是把Google reCAPTCHA widget render了出来，同时通过postMessage将reCAPTCHA放回的结果送给React Native。好了，就是如此简单。

当然，为了方便其他人的使用，我已经publish一个[npm package](https://www.npmjs.com/package/rn-recaptcha)提供给大家使用。只要简单的

```
  yarn add rn-recaptcha
  npm install rn-recaptcha
```

就ok了。这里是link [rn-recaptcha](https://www.npmjs.com/package/rn-recaptcha)

下边是具体的一个demo gif。 Happy Hacking :) ![demo]({{url}}/resources/img/rn-1.gif)

