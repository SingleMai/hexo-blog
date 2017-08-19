---
title: HTML5美化版复选框和单选框
date: 2017-05-23 22:17:08
categories: "html+css"
tags:
    - demo
---
今天的demo是实现复选框和单选框的美化。主要涉及的是css3的利用，但是弥补了自己有关`<label>`标签知识点的不足。还蛮有收获。通过超链接还找到了好多种其他的实现，可以每天都练练手嘿。那个开关按钮！！！一定要学，感觉可以运用到我的照片墙里面。
<!-- more -->
## 知识弥补
### `<label>`标签
#### 定义与用法
<label> 标签为 input 元素定义标注（标记）。
label 元素不会向用户呈现任何特殊效果。不过，它为鼠标用户改进了可用性。如果您在 label 元素内点击文本，就会触发此控件。就是说，当用户选择该标签时，浏览器就会自动将焦点转到和标签相关的表单控件上。
<label> 标签的 for 属性应当与相关元素的 id 属性相同。
#### 提示与注释：
**注释**："for" 属性可把 label 绑定到另外一个元素。请把 "for" 属性的值设置为相关元素的 id 属性的值。

当点击的有for属性的label标签时，对应的Checkbox复选框会被选中。这意味着，我们可以通过label的点击事件来处理我们的Checkbox复选框。实现动画效果。

### 有关:before、:after伪类
要使这两个类生效（不设置文字，只需要样式）也一定要添加一句。不然永远都不会生效哈。
```
content:'';
```

### 隐藏默认复选框样式：
```
input[type=checkbox] {
	visibility: hidden;
}
```

弄懂上面的知识之后就可以开搞啦！！涉及的就是如何利用各种css3的属性进行界面的美化了。
## 开搞！
### html页面
html的样式。本来学习之前，我自己的想象会这个复杂得多。但是发现善用伪类可以让你方便操作标签，也不会造成html页面过于复杂。写过三四个样式后发现，这只要善用伪类，真的这两个标签足矣。
```

<section>
  <!-- Checbox One -->
  <h3>Checkbox One</h3>
  	<div class="checkboxOne">
  		<input type="checkbox" value="1" id="checkboxOneInput" name="" />
	  	<label for="checkboxOneInput"></label>
  	</div>
</section>
```
### css3
这里才是重头戏的开始。首先给父级设置定位属性，方便后面子类位置的调整。
规定框的大小。当然这些内容耦合性有点高，如果能引入sass进行编码。那样就能实现组件化了。（不过这个应该都是这样哈，自己废话）
```
.checkboxOne {
	width: 40px;
	height: 10px;
	background: #555;
	margin: 20px 80px;
	position: relative;
	border-radius: 3px;
}
```
现在，我们可以把label作为条带上的滑块，我们希望按钮效果是从条带的一侧移动到另一侧，我们可以添加label的过渡。简化起见，一概忽略各种前缀的存在。
`transition`是实现动画的关键所在。
```
.checkboxOne label {
	display: block;
	width: 16px;
	height: 16px;
	border-radius: 50%;

	transition: all .5s ease;
	cursor: pointer;
	position: absolute;
	top: -3px;
	left: -3px;

	background: #ccc;
}
```
现在这个滑块在选中（关闭）位置，当我们选中复选框，我们希望有一个反应发生，所以我们可以移动滑块到另一端。我们需要知道，判断复选框被选中，如果是则改变label元素的left属性。
```
.checkboxOne input[type=checkbox]:checked + label {
	left: 27px;
}
```
这样就大功告成啦。剩下的内容都是各种`background`属性，各种阴影，各种`box-shadow`来实现立体效果。
## 总结一下
其实实现原理并不复杂。复杂的是如何去利用`background`属性进行颜色搭配美化。
### 利用内边框阴影
```
box-shadow: inset 0px 1px 1px rgba(0, 0, 0, 0.5), 0px 1px 0px rgba(255, 255, 255, 0.2);
```
### 利用`background`属性
```
background: linear-gradient(to bottom, #fcfff4 0%, #dfe5d7 40%, #b3bead 100%);
```

### 利用字体阴影
```
text-shadow: 1px 1px 0px rgba(255, 255, 255, 0.15);
```

### 利用透明度实现悬停提示效果
也是看别人用到了，才发现这里有兼容性问题。参考至[CSS - firefox与IE透明度(opacity)设置区别]()
#### IE：
`filter:alpha(opacity=sqlN)`
其中 `sqlN`的值域为[0, 100]

js: `ieNode.style.filter="alpha(opacity=sqlN)"; `

### Firefox：/*参考,不推荐使用*/
`-moz-opacity:sqlN`
其中`sqlN`的值域为[0, 1]

### Firefox,Chrome和Safari：
`opacity:sqlN`
其中`sqlN`的值域为[0, 1]

js:` firefoxNode.style.opacity=sqlN; `   

```
filter: progid:DXImageTransform.Microsoft.Alpha(enabled=false);
opacity: 1;
```
参考至[CSS - firefox与IE透明度(opacity)设置区别](http://www.cnblogs.com/silence516/archive/2010/01/24/ie_ff_opacity.html)

### 利用旋转
`transform: rotate(-45deg);`

## 结语
嘻嘻。今天学有所获学有所获。而且看似复杂的css文件，其实都是在利用上面的各种属性不同搭配来实现效果。善于利用组合一定能做到更美！然后要找机会学那个开关按钮！
> best for best

来源：[10个HTML5美化版复选框和单选框](http://geek.csdn.net/news/detail/198571?utm_source=tuicool&utm_medium=referral)
