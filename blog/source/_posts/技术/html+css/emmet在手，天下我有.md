---
title: emmet在手，天下我有
date: 2017-08-17 17:55:10
categories: 'html+css'
tags:
---
以前的理解是个方便生成一些代码片段的小插件，一直觉得是方便了不少，但是也没觉得怎样。今天稍微一查他的高级应用，简直惊呆了我和我的小伙伴。
<!--more-->
`emmet`的使用，估计每一个前端人员都必须掌握的插件。
`.box>img.box-img>a[href='javascript:void(0)']#id1*3`
如果上面这段东西就已经让你欣喜若狂了的话。那你肯定要继续往下看！如果你居然看不懂，那建议先去补补它的基础入门，三分钟学会。

因为都是些方便性类似快捷键的操作，就直接抛技能点好了
## 生成自定义属性
直接用中括号包起你想要添加到标签的属性
`a[href="javascript:void(0)" title="我爱妞妞"]`

## 对生成内容编号：$
是不是写demo的时候，或者写下面这坨代码的时候，每次的修改都让你筋疲力竭。
```
<ul>
 <li class="item item1"></li>
 <li class="item item2"></li>
 <li class="item item3"></li>
 <li class="item item4"></li>
 <li class="item item5"></li>
</ul>
```
没关系，一键解决！
```
ul>li.item.item$*5
```
就这样的一段东西，直接解决你的痛点

什么？你要倒序，54321？那你就这样
```
ul>li.item.item@-$*5
```
你还想要规定数字开始？这个就是从3开始计数
```
ul>li.item.item@3*5
```
我觉得你有这种需求下面的需求已经是丧心病狂了。但是emmet还是能帮你实现
```
// ul>li.item.item@-3*5
<ul>
    <li class="item7"></li>
    <li class="item6"></li>
    <li class="item5"></li>
    <li class="item4"></li>
    <li class="item3"></li>
</ul>
```

## 生成文本内容：{}
这时候你开始大胆起来了，要求他帮你实现文本放置。一个`{}`搞掂，和`[]`使用一致

## css属性
大概已经不亦乐乎了吧，但是emmet还在努力解决你的所有痛点。
解决你添加前缀的痛点！那如果想要指定前缀呢？试试`-wm`或者`-wmo`你就会知道啦
```
// -foo-css:
-webkit-foo-css: ;
-moz-foo-css: ;
-ms-foo-css: ;
-o-foo-css: ;
foo-css: ;
```

## 添加图片的尺寸大小
之前一直在用atom搜索这样的一个辅助插件，可以免去频繁地切换出去找图片尺寸大小。实在没想到，功能其实早就有了，只是我没看到它。
```
ctrl + u //直接读取图片的尺寸大小，帮你添加到相应位置
```

## 更新css属性
每次为了hack一个属性会有多个前缀，然后改一个就要改全部，好麻烦的说。现在一个快捷键解决，先改一个，按一下，全部改完。美滋滋
```
ctrl + shift + R
```

## 图片转base64
这个不常用，主要还是因为base64看起来真的让人好不习惯。
```
ctrl + ‘
```

-----------------------------
参考
[Emmet 常用的高级功能](http://blog.wpjam.com/m/emmet-grammar/)
