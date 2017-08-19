---
title: CSS的权重问题
date: 2017-08-10 23:37:42
categories: 'html+css'
tags:
---
在大概5月份6月份的时候吧，看了一篇文章讲的是CSS的权重问题。那时候恍然大悟，突然感觉自己实在是懂太少。
<!--more-->
## 什么是权重
权重就是统计学经常用的术语，用来衡量一个数据的重要性。那在CSS中权重是什么就很明显了。
- 权重决定了CSS规则怎样被浏览器解析直到生效。“css权重关系到你的css规则是怎样显示的。”
- 当很多的规则被应用到某一个元素中，权重是一个决定哪种规则生效，或者是优先级的过程。
- 每个选择器都有自己的权重。你的每条css规则，都包含一个权重级别。这个级别是由不同的选择器加权计算的，通过权重，不同的样式最终会作用到你的网页中
- 如果两个选择器同时作用到一个元素上，权重高者生效。
- 如果两个选择器权重一样，那么最后定义的规则覆盖前面的规则。

## 权重等级
每个选择器在权重级别中都有自己泾渭分明的位置。根据选择器种类的不同可以分为四类，页决定了四中不同等级的权重值。

### 行内样式，指的是html文档中定义的style
行内样式包含在你的html中，对元素产生直接作用。

### ID选择器
id也是元素的一种表示，比如`#div`

### 类，属性选择器和伪类选择器
这一类包括各种`class`，属性选择器，伪类选择器。比如`:hover`,`:focus`等等

### 元素和伪元素
元素跟伪元素选择器，比如`:before`与`:after`

## 怎么确定权重
权重记忆口诀。从0开始，一个**行内样式+1000**，一个**id+100**,一个**属性选择器/class或者伪类+10**

好了，就这样。本质上，权重已经讲完了。练习题
```
body #content .data img:hover // 122
*{} // 0
li{} 1
li:first-line{} //2
ul li{} 2
```

## 关于权重的建议
永远不要使用!important
减少选择器的个数

http://www.w3cplus.com/css/css-specificity-things-you-should-know.html

https://www.smashingmagazine.com/2007/07/css-specificity-things-you-should-know/

http://www.htmldog.com/guides/css/intermediate/specificity/
