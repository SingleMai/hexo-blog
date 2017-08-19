---
title: 利用JS为CSS3属性添加前缀
date: 2017-08-10 23:37:42
categories: 'html+css'
tags:
---
利用js智能地为css3的新属性添加前缀实现兼容性写法。
<!--more-->
以前从来没考虑过，如果一个需要添加前缀的CSS属性，是用js添加的，那么如何绑定前缀。想想要写一大坨代码就有点恶心人。这时候总会想起CSS预编译的好处。同样还是慕课网嘻嘻，带给我个新思路。

只需要抓浏览器的一个新建div，他里面就有封装一个浏览器名字的属性。借由这个属性，我们可以很简单地找到我们需要添加何种前缀。这样友好且没有多余的代码。

这样一看可能还一脸懵逼，建议直接到浏览器，跑下面代码观察一下div标签里面的属性构成你就懂了哦。

```
// 创建一个DIV对象并获取它的所有的style属性
let elementStyle = document.createElement('div').style
let transformNames = {
  webkit: 'webkitTransform',
  Moz: 'MozTransform',
  O: 'OTransform',
  ms: 'msTransform',
  standard: 'transform'
}
for key in transformName {
  if (elementstyle[transformName[key]] !== undefind) {
    return key
  }
}
```
