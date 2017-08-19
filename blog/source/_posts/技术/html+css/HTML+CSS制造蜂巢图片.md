---
title: HTML+CSS制造蜂巢图片
date: 2017-05-22 19:27:11
categories: "html+css"
tags:
    - demo
---
想想最近几个月，学了node、学后端逻辑、学vue。感觉已经好久没写页面，没写html+css了。所以就找了个小demo来做——实现蜂巢图片。
<!-- more -->
## 前面一些废话
当然也是给自己以后的一个想法做准备吧,想制作一个照片墙的网站。只要在文件夹中添加图片，网站就能自动生成图片排版，然后展示出来。当然最重要的就是点击以后可以出现图片的详情——甚至有介绍。感觉用来当日记形式的照片墙。不过这些都是后话啦，如果有可能，希望在今年完成吧。

那么第一步就是尝试各种照片的排版效果啦！感觉用蜂巢也能做出点感觉来。所以就尝试了这个deomo。

## 实现原理
参考至[教你做炫酷的蜂巢式图片墙](http://www.cnblogs.com/ghost-xyx/p/3788438.html)
主要利用了三个长方形，通过旋转来组合形成蜂巢。相比菱形等会相对简单。没有涉及透明的部分。三个div分别用上一样的图片，然后设置overflow等属性实现。相对简单，所以直接贴源码。

### html
```
<div class="honeycomb">
  <div class="first">
    <img class="firstImg" src="./img-demo/1.jpg" width="173px" height="200px">
  </div>
  <div class="second">
    <img class="secondImg" src="./img-demo/1.jpg" width="173px" height="200px">
  </div>
  <div class="last">
   <img class="lastImg" src="./img-demo/1.jpg" width="173px" height="200px">
  </div>
</div>
```
### css
```
.honeycomb{
  margin: 100px;
}
.first,.second,.last{
  width: 173px;
  height: 100px;
  position: absolute;

  overflow:hidden;

  border-left: 2px solid #ff8b8b;
  border-right :2px solid #ff8b8b;
}
.second{
 -webkit-transform:rotate(60deg);
}
.last{
 -webkit-transform:rotate(-60deg);
}
.firstImg,.secondImg,.lastImg{
   margin-top: -50px;
}
.secondImg{
  -webkit-transform:rotate(-60deg);
}
.lastImg{
  -webkit-transform:rotate(60deg);
}
```
