---
title: CSS布局技巧之垂直水平居中
date: 2017-09-4 23:37:42
categories: 'html+css'
---
还记得我进公司实习的第x天，领导在电梯和我说。页面重构布局，只要学会这几招就能打遍天下了：“垂直水平居中”、“深入理解盒子模型概念”、...还有一个居然忘了。Anyway，现在我们就来看一下花式实现垂直水平居中。
<!--more-->
## 第一式，`margin: 0 auto`（水平居中）
这招是人手必备的水平居中技巧，适用于正常文档流（不包括浮动和定位）的块状标签居中方法。
### 第二式，`text-align:center`（水平居中）
这招同样也是人手必备的，适用于正常文档流的行内块状和内联标签
### 第三式，`display:table-cell`（垂直水平居中）
通过将元素模拟成`table`标签来运用`table`所独有的居中和垂直居中效果。注意父元素需要`display:table`，在子元素`display:table-cell;text-align:center;vertical-align:middle;`
### 第四式，适用绝对定位来进行居中（垂直水平居中）
这一招为了兼容性的话，需要知道元素的宽度或高度。
```
position: absolute;
left: 50%;
margin-left: -50px
width: 100px;
```
如果你熟悉css3的新属性，那么肯定能够想到。`transform:translateX(-50%)`这样子可以不考虑元素的宽度高度就能实现绝对居中，但是有兼容性问题。

### 第五式，`line-height`实现单行文本居中（垂直居中）
只需要将文本的行高设置到和父级容器高度一致即可。

### 第六式，同样利用绝对定位实现居中（垂直水平居中）
有两个缺点：兼容性问题，兼容ie9以上；需要知道元素的宽高。足足有两个缺点，那为什么我们要使用呢？（其实我也不知道，但是多一招总没错）
```
.parent{
  position: relatvie;
  width: 200px;
  height: 200px;
}
.child{
  position: absolute;
  width: 100px; //必须固定
  height: 100px; //必须固定
  left: 0; // left 和 right 必须成对出现实现水平居中
  right: 0;
  top: 0;
  bottom: 0;
  margin: auto; // 必须出现
}
```
### 第七式，使用浮动配合相对定位进行水平居中（水平居中）
此方法是关于浮动元素怎么水平居中的解决方法，并且不需要知道需要居中的元素的宽度。

浮动居中的原理是：把浮动元素相对定位到父元素宽度50%的地方，但这个时候元素还不是居中的，而是比居中的那个位置多出了自身一半的宽度，这是就需要他里面的子元素再用一个相对定位，把多出的自身一半的宽度拉回来，而因为相对定位正是相对于自身来定位的，所以自身一半的宽度只要把left或right设为50%就可以了。

这种使用浮动配合相对定位来居中的方法，优点是不用知道要居中的元素的宽度，即使这个宽度是不断变化的也行；缺点是需要一个多余的元素来包裹要居中的元素。而且不能实现垂直居中。

```
.block {
  width: 500px;
  height: 500px;
  background: green;
}
.wraper{
  float: left;
  position: relative;
  left: 50%;
  clear: both;
}
.content{
  position: relative;
  left: -50%;
}
```

变形后，失去兼容性，但是不需要多套一层div。只是不知道和直接用绝对定位的区别。
```
.block {
  width: 500px;
  height: 500px;
  background: green;
}
.content{
  float: left;
  position: relative;
  left: 50%;
  transform: translateX(-50%);
  clear: both;
}
```

### 第七式，flex布局（垂直，水平）
这个除了兼容性，绝对是布局必备。阮一峰大大的两篇博客都写得很好，直接参考着就能学会了。这里不重复说。
