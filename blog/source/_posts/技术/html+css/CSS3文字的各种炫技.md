---
title: CSS3文字的各种炫技
date: 2017-09-12 10:37:42
categories: 'html+css'
tags:
---
本来是打算回顾一下之前学习过的内容，稍微拾起一些忘掉的知识，没想到在看到了[Fun with line-height!](https://css-tricks.com/fun-line-height/),才发现我以前对网页的字体实在是太悲观了（主要是在实习公司往往要求兼容性）
<!-- more -->

<style>
  .demo-bg{
    background: black;
  }
  .demo-text{
    text-align: center;
    -webkit-background-clip: text;
    color: transparent;
    width: 500px;
  }
  .demo-hollow{
    -webkit-text-stroke: 1px grey;
    font-size: 60px;
  }
  .demo-color-text{
    background-image: linear-gradient(to bottom, #9588DD, #9588DD 22px, #DD88C8 22px, #DD88C8 44PX, #D3DD88 44PX, #D3DD88 66PX, #8880DD 66PX, #8880DD);
    line-height: 22px;
  }
  .demo-hide-text{
    background-image: linear-gradient(
      to top,
      rgba(255,255,255, 0.2) 22px,
      rgba(255,255,255, 0.4) 22px,
      rgba(255,255,255, 0.6) 44px,
      rgba(255,255,255, 0.6) 66px,
      rgba(255,255,255, 0.8));
    background-position: bottom center;
    line-height: 22px;
  }
  .demo-list{
    position: relative;
    width: 400px;
    font-size: 25px;
  }
  .demo-top, .demo-bottom{
    position: absolute;
    width: 100%;
    height: 50px;
    pointer-events: none;
  }
  .demo-top{
    background-image: linear-gradient(to bottom, #fff , rgba(255,255,255,0.6));
  }
  .demo-bottom{
    bottom: 0;
    background-image: linear-gradient(to top, #fff , rgba(255,255,255,0.6));
  }
</style>

## text-fill-color
我们知道在css中，如果想要定义字体颜色就要用`color`属性。在初学的时候，我总是觉着这个属性不语义化，但是久而久之就习惯了。没想到，除了IE其他浏览器还实现了这么一个属性`text-fill-color`。那么这个属性存在的意义是什么呢?

在网上找了很多篇文章：
1. `text-fill-color`可以使用透明色，额..经测试现阶段已经没有这个顾虑。`color`同样可以。
2. `text-fill-color`结合使用`text-stroke`可以实现镂空文字，流光文字..额依旧是失败，因为主要还是利用到了`transparent`，所以由于第一点解决了，这一点也不成立了。

### 这里有一个小点，或许可以用上？
`text-fill-color`可以覆盖`color`属性的颜色，也就是如果对文字同时设置这两个属性，浏览器会应用前者的颜色。

综上所述，两者暂时没有太大差别了。相反，`texct-fill-color`反而在IE下不被支持，那么还是用回color属性吧~

## text-sroke 文字描边
在以往的经验中，往往每次设计师在字体上下功夫，总会苦恼为啥边框可以描边，文字却不行~没想到不是不行，只是自己认识太短浅。两个属性轻松实现镂空文字~（坏消息：IE依旧不支持，需要加前缀`-webkit`）
- 使用起来就和`border`一样
- `text-stroke:1px transparent`可以使文字更柔和
```
-webkit-background-clip: text;
color: transparent;
-webkit-text-stroke: 1px grey;
```
效果如下：

<p class="demo-text demo-hollow">镂空文字</p>

## background-clip: text;
在以往的经验中，往往只使用过`background-clip: content-box`,几乎从来没听说过它有`text`这个选项。不过没听过的正常，因为这里需要添加前缀`-webkit-`来使用，兼容性还是IE爸爸不支持

有使用过该属性的就知道了，这是指将背景应用范围限定在文本上~~于是结合渐变背景，分分钟实现文字渐变效果。

## background-image: linear-gradient()
这里IE爸爸的兼容性终于有方案了，使用特有的`filter`属性。这里的属性具体怎么填稍微复杂一点，所以建议查文档比较好。

## 组合效果
### 彩色文字
利用渐变色的硬性转色，实现每一行文字的定制化颜色，但是实用性不强，毕竟需要仔细去定义每一行的颜色，然而文字的数量行数大多时候又不可控~~（PS:需要和`line-height`配合使用，所以如果使用CSS预编译器会更好。

<div class="demo-bg"><p class="demo-text demo-color-text">The unhandledrejection event is fired when a Promise is rejected but there is no rejection handler to deal with the rejection.The unhandledrejection event is fired when a Promise is rejected but there is no rejection handler to deal with the rejection.</p></div>

### 文本末尾渐隐
根据`line-height`来实现文本最后几行渐隐显示，这个在各大APP上都能看到这种效果。很不错，其实也是这些属性的组合。当然，等下再介绍另一种实现方式。
```
.demo-hide-text{
  background-image: linear-gradient(
    to top,
    rgba(255,255,255, 0.2) 22px,
    rgba(255,255,255, 0.4) 22px,
    rgba(255,255,255, 0.6) 44px,
    rgba(255,255,255, 0.6) 66px,
    rgba(255,255,255, 0.8));
  background-position: bottom center;
  line-height: 22px;
}
```

<div class="demo-bg"><p class="demo-text demo-hide-text">The unhandledrejection event is fired when a Promise is rejected but there is no rejection handler to deal with the rejection.The unhandledrejection event is fired when a Promise is rejected but there is no rejection handler to deal with the rejection.</p></div>

在参考文中还有其他实现方式，但是都觉得不是很好看不适用，所以这里不再详细叙述~~

## `pointer-events:none`不可点击，取消事件
这简直是一个神一般实用的属性。兼容到ie11，嗯嗯，还不赖。文末有张鑫旭老大的介绍文章，so这里也不详细说明。大致就是一个可以取消鼠标任何点击事件的一个css，在工作中当你需要对一个按钮暂时地禁用点击，总是不可避免要用js进行控制，十分麻烦。而这个属性可以直接让按钮取消掉事件的点击，完全不可点。当然，只是鼠标不可以点击（可以用键盘tab取消焦点），老大在文章中提到了解决方案...同理不详细叙述

“幻影”用来形容这个属性最恰当不过了，设置了该属性的元素，就成为了一个幻影，不接受任何的点击，而且接受点破。。也就是用来实现遮罩渐隐效果。see deomo!首尾一个标签绝对定位，之后利用渐变覆盖，然后设置不可点击——转化成幻影
```
<ul class="demo-list">
  <li class="demo-item demo-top"></li>
  <li class="demo-item">拔萝卜拔萝卜拔萝卜拔萝卜拔萝卜拔萝卜拔萝卜拔萝卜拔萝卜拔萝卜拔萝卜拔萝卜拔萝卜</li>
  <li class="demo-item">小兔子小兔子小兔子小兔子小兔子小兔子小兔子小兔子小兔子小兔子小兔子小兔子小兔子</li>
  <li class="demo-item demo-bottom"></li>
</ul>
```

<ul class="demo-list"><li class="demo-item demo-top"></li><li class="demo-item">拔萝卜拔萝卜拔萝卜拔萝卜拔萝卜拔萝卜拔萝卜拔萝卜拔萝卜拔萝卜拔萝卜拔萝卜拔萝卜</li><li class="demo-item">小兔子小兔子小兔子小兔子小兔子小兔子小兔子小兔子小兔子小兔子小兔子小兔子小兔子</li><li class="demo-item demo-bottom"></li></ul>

今天就差不多这里啦~~~嗯嗯，从line-height一直到学到这么多，今天还不错~明天继续努力
----------------
参考文章：
[Fun with line-height](https://css-tricks.com/fun-line-height/)
[CSS3 text-fill-color简介及应用展示](http://www.zhangxinxu.com/wordpress/2010/06/css3-text-fill-color%E7%AE%80%E4%BB%8B%E5%8F%8A%E5%BA%94%E7%94%A8%E5%B1%95%E7%A4%BA/)
[CSS3 pointer-events:none应用举例及扩展](http://www.zhangxinxu.com/wordpress/2011/12/css3-pointer-events-none-javascript/)
[CSS实现兼容性的渐变背景(gradient)效果](http://www.zhangxinxu.com/wordpress/2010/04/css%E5%AE%9E%E7%8E%B0%E5%85%BC%E5%AE%B9%E6%80%A7%E7%9A%84%E6%B8%90%E5%8F%98%E8%83%8C%E6%99%AFgradient%E6%95%88%E6%9E%9C/)
