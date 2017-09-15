---
title: HTML常用meta头
date: 2017-05-28 16:06:43
categories: 'html+css'
tags:
---
每一个页面结构总有一堆的meta会被复制进去，每次写到都是直接copy，那到底每一个meta头有什么作用呢？一起来理一下~~
<!-- more -->
在公司实习时候就规定了两套meta模板~一个PC端一个V端。
### PC端规范
```
<!-- 腾讯CP规范 -->
<meta charset="gbk">
<meta name="robots" content="all">
<meta name="author" content="Tencent-CP">
<meta name="Copyright" content="Tencent">
<meta name="Description" content="">
<meta name="Keywords" content="腾讯游戏">
```
### 移动端规范
```
<meta charset="gb2312">
<meta name="applicable-device" content="mobile">
<meta name="viewport" content="width=device-width,initial-scale=1,maximum-scale=1,user-scalable=0,maximum-scale=1">
<meta name="viewport" content="width=320,minimum-scale=0,maximum-scale=5,user-scalable=no">
<meta name="format-detection" content="telephone=no">
<meta content="yes" name="mobile-web-app-capable">
<meta content="yes" name="apple-mobile-web-app-capable">
<meta name="robots" content="all">
<meta name="author" content="Tencent-TGideas">
<meta name="Copyright" content="Tencent">
<meta name="Description" content="">
```

### 详
#### 字符编码
`<meta charset="gbk">`
#### 文件可以被检索
`<meta name="robots" content="all">`
#### 作者
`<meta name="author" content="">`
#### 声明版权所有
`<meta name="Copyright" content=""`
#### 描述
`<meta name="description" content=""`
#### 关键字
`<meta name="keywords" content=""`
#### 优先使用IE最新版本和Chrome
` <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />`
#### 网页适合在什么设备上浏览
```
<meta name="applicable-device" content="mobile"> // 手机端
<meta name="applicable-device" content="pc"> // PC端
<meta name="applicable-device" content="pc, mobile"> //双端自适应
```
#### 为移动设备添加viewport
```
<meta name="viewport" content="initial-scale=1,maximum-scale=3,minium-scale=1,user-scalable=no">
<!-- width=device-width 会导致 iPhone 5 添加到主屏后以 WebApp 全屏模式打开页面时出现黑边 http://bigc.at/ios-webapp-viewport-meta.orz -->
```
#### IOS设备
添加到主屏后的标题
`<meta name="apple-mobile-web-app-title" content=""`
是否启用WebApp全屏模式
`<meta name="apple-mobile-web-app-capable" content="yes" />`
设置状态栏的背景颜色
`<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent"`(只有在开启webapp全屏才生效)
禁止数字识别为电话号码
`<meta name="format-detection" content="telephone=no" />`

#### Android
控制选项卡颜色（浏览器标签页选项）
`meta name="theme-color" content="#db5945"`

外链：
[html常用meta头](http://www.runoob.com/w3cnote/html-meta-intro.html)
