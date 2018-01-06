---
title: HTML经典布局
date: 2017-09-16 10:37:42
categories: 'html+css'
tags:
  - '持续收录'
---
前天..面试腾讯的一个前端岗，觉得自己没有好好准备充分啊。。。我觉得任何布局，我都能瞬间解答。然而，考官问我——圣杯布局，懂吗？！我内心瞬间崩溃...我只是懂得这个名字，具体不知道这个布局是要做什么。。然后接下来的布局知识几乎因为这个而全部慌乱得不行...惭愧惭愧
<!-- more -->
ps:
1. 这里原本是贴点demo会更好，但是考虑到内容多，而且都是很容易在网上找到样子的。所以还是直接贴代码大伙自己拷回去研究把。
2. 持续收录型~~
## 圣杯布局与双飞翼布局
其实细致点看要求，第一反应就是这两个布局明明就是一个东西啊。但是可能是由于前端工程师对于这个东西已经有一套被最优化的代码了吧？所以只有这样实现，才叫圣杯，只有这样才叫双飞翼。这其实是我不太喜欢的一种思维固化，但是奈何考官不这么想啊~~他只会觉得你用别的野路子实现就会出BUG。所以还是认真地实现一次传统的圣杯和双飞翼并区分一下把。

### 圣杯布局
利用的属性： 浮动、margin负边距、相对定位。
头部尾部占满一行，中间分成三栏布局。左右都是固定宽度，中间自适应。可以让中间内容区先行渲染。
**但是，圣杯布局有个问题，当面板的main部分比两边子面板宽度小的时候，布局就会乱掉**
```
css:
body{
  margin: 0;
  padding: 0;
}
header, footer{
  width: 100%;
  background: red;
}
/* flex 圣杯布局  */
/*.middle-box{
  display: flex;
  width: 100%;
  background: green;
}
.middle-box .left{
  flex: 0 0 200px;
  width: 200px;
  height: 200px;
  background: pink;
}
.middle-box .middle{
  flex: 1;
  width: 100%;
  height: 200px;
}
.middle-box .right{
  flex: 0 0 200px;
  width: 200px;
  height: 200px;
  background: pink;
}*/
/* 不使用flex */
/*.middle-box{
  padding: 0 200px;
  width: 100%;
  background: pink;
  overflow: hidden;
}
.middle-box .left, .middle-box .right{
  position: relative;
  float: left;
  width: 200px;
  height: 200px;
  background: pink;
}
.middle-box .left{
  margin-left: -100%;
  left: -200px;
}
.middle-box .middle{
  float: left;
  width: 100%;
  height: 200px;
  background: green;
}
.middle-box .right{
  right: 200px;
  margin-left: -200px;
}*/

html:
<!-- 圣杯布局 -->
<!-- <header>header</header>
<div class="middle-box">
  <div class="middle">
    <h1>middle</h1>
    <h1>middle</h1>
  </div>
  <div class="left">left</div>
  <div class="right">right</div>
</div>
<footer>footer</footer> -->
```
### 双飞翼布局
双飞翼布局其实也是三栏布局的场景，但是dom结构不一样，样式不一样，利用的技术也不一样。所以名字也不一样。
利用属性： 浮动、margin负边距、多用一个标签

其实从代码量上看，双飞翼更加简洁优雅，而且响应式更好，所以可以理解为双飞翼是更好的圣杯布局。
```
css:
/* 三栏布局 双飞翼布局 */
.double-box{
  width: 100%;
  overflow: hidden;
}
.double-box .left,.double-box .middle, .double-box .right{
  float: left;
}
.double-box .left{
  width: 20%;
  background: pink;
  margin-left: -100%;
}
.double-box .right{
  width: 30%;
  background: green;
  margin-left: -30%;
}
.double-box .middle{
  width: 100%;
  background: red;
}
.double-box .main{
  margin: 0 30% 0 20%;
}
html:
<!-- 三栏布局 双飞翼布局 -->
<div class="double-box">
  <div class="middle"><div class="main">middle</div></div>
  <div class="left">left</div>
  <div class="right">right</div>
</div>
```

## 两栏布局
利用属性： 浮动、margin负边距
```
css:   
/* 两栏布局 右边固定*/
/*.box{
  width: 100%;
  overflow: hidden;
}
.box .right{
  float: right;
}
.box .right{
  width: 100px;
  height: 100px;
  background: green;
}
.box .main{
  width: 100%;
  height: 100px;
  background: pink;
  padding-right: 100px;
  box-sizing: border-box;
}*/
/* 两栏布局 右边固定flex */
/*.box{
  display: flex;
}
.box .right{
  flex: 0 0 100px;
  background: green;
}
.box .main{
  flex: 1;
  background: pink;
}*/
/* 两栏布局 左边固定 */
.box{
  width: 100%;
  overflow: hidden;
}
.box .left{
  float: left;
  width: 100px;
  background: pink;
}
.box .main{
  width: 100%;
  margin-left: 100px;
  background: green;
}
html:
<!-- 两栏布局 左边固定  -->
<!-- <div class="box">
<div class="left">left</div>
<div class="main">content</div>
</div> -->
<!-- 两栏布局 右边固定 -->
<!-- <div class="box">
<div class="main">content</div>
<div class="right">left</div>
</div> -->
```
## 瀑布流布局
有大量图片的网站往往会使用瀑布流布局，实现原理很简单，需要搭配js代码进行控制。

利用绝对定位，动态计算屏幕的列数，已经图片需要展示的位置。具体实现代码量有点大。大致思路就是：先获取屏幕宽度，计算显示多少列，然后计算每一列显示的高度。再用一个以列数为长度的数组来存储当前每一列的已有高度，进行动态插入。

### 利用CSS3 column属性
利用column属性也可以实现瀑布流效果。而且简便一点，但是缺点就是纵向展开，向右推进。和我们期望得到的用户体验效果不一致，所以不建议使用。
（也就是说页面图片先向下排列，排满一列再排第二列，而不是横向排序）

## flex布局
弹性盒子，简直是布局界的神器，灵活运用好每一个属性，可以说可以十分渐变快捷地解决大部分布局问题。面试的时候一直用这招...然而面试我的考官居然说这样有问题？ 我猜是兼容性问题吧？如果说是页面错乱的问题...我可能会有点不服。。。
