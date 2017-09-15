---
title: CSS3高斯模糊与毛玻璃
date: 2017-09-11 10:37:42
categories: 'html+css'
tags:
---
今天看了一篇有关于毛玻璃的推送，才发现自己之前眼拙，没看出来是毛玻璃效果，单纯用了透明度实现，实在愧疚愧疚....
<!-- more -->
<style>
  .list {
    position: relative;
    width: 160px;
    height: 140px;
    cursor: pointer;
    overflow: hidden;
  }
  .layer {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    transition: all .25s;
  }
  .cover{
    position: absolute;
    left: 0;
    bottom: 0;
    width: 100%;
    height: 30px;
    line-height: 30px;
    overflow: hidden;
    transition: all .25s;
  }
  .blur{
    position: absolute;
    left: 0;
    bottom: 0;
    filter: blur(5px);
    width: 100%;
    transform: translateZ(0);
  }
  .mask{
    position: absolute;
    left: 0;
    bottom: 0;
    width: 100%;
    background: rgba(0,0,0,.3);
    filter: none;
    color: #fff;
    font-size: 14px;
    text-indent: 2em
  }
  .layer:hover, .layer:hover + .cover{
    transform:scale(1.1);
  }
</style>

先上例子吧~~

<div class="list"><img src="http://img0.bdstatic.com/img/image/shouye/xinshouye/mingixng204.jpg" class="layer"><div class="cover"><img src="http://img0.bdstatic.com/img/image/shouye/xinshouye/mingixng204.jpg" class="blur"><div class="mask">明星</div></div></div>

就大概是介个样子的。细心看一下，就会发现，相比单纯用透明色来遮罩，还是高斯模糊显得牛逼轰轰呐~~

其实有关css的特效，更多是属性组合之类的，所以，直接看源码就好了吧~~用控制台查看。

这里有两位大佬的文章，可以查看着用：
[高斯模糊的算法](http://www.ruanyifeng.com/blog/2012/11/gaussian_blur.html)
[纯CSS实现易拉罐3D滚动效果](http://www.zhangxinxu.com/wordpress/2010/03/%E7%BA%AFcss%E5%AE%9E%E7%8E%B0%E6%98%93%E6%8B%89%E7%BD%903d%E6%BB%9A%E5%8A%A8%E6%95%88%E6%9E%9C/)
[使用CSS将图片转换成模糊(毛玻璃)效果](http://www.zhangxinxu.com/wordpress/2013/11/css-svg-image-blur/)
