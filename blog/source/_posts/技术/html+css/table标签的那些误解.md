---
title: table标签的那些误解
date: 2017-08-01 19:27:11
categories: "html+css"
---
`table`布局这个词语好像是上个世纪的东西一样。我居然完全不认识。
<!-- more -->
学习的时候从来没听说过`table`布局，偶尔听说`table`标签的情况下都是那些"`table`标签能不用就不用"。然而，最近做的一个需求，隔壁小哥哥强烈推荐我用`table`，说不然会出现各种奇奇怪怪乱七八糟的bug，不好处理。于是乎，我就开始学着使用这个一直处于听说状态的标签。但是用了之后，发现它很舒服啊，自适应的布局。轻松地实现居中（除了html结构实在是太多标签要写了）

于是就开始疑惑了。为什么`table`标签不被推荐使用了呢？明明他还是有他很明显的优点的啊，为什么就不被推崇了呢。查了一下资料，大概有些懂了。

**不被推崇的是`table`布局，而不是`<table>标签。`**

这两者还是有很大区别的。`table`布局，指的是`table+css`进行页面排版布局。而`<table>`标签则是专门为表格设计的一个标签。所以遇到表格用`<table>`，反正我也没学过什么`table+css`的布局方式，那倒直接免去区分什么情况用`table布局`什么时候`div布局`的纠结问题了。

之后再贴一些`table`布局不被推崇的原因：
1. Table要比其它html标记占更多的字节。
(延迟下载时间，占用服务器更多的流量资源。)
2. Tablle会阻挡浏览器渲染引擎的渲染顺序。
(会延迟页面的生成速度，让用户等待更久的时间。)
3. Table里显示图片时需要你把单个、有逻辑性的图片切成多个图。
(增加设计的复杂度，增加页面加载时间，增加HTTP会话数。)
4. 在某些浏览器中Table里的文字的拷贝会出现问题。
(这会让用户不悦。)
5. Table会影响其内部的某些布局属性的生效(比如<td>里的元素的height:100%)
(这会限制你页面设计的自由性。)
6. 一旦学了CSS知识，你会发现使用table做页面布局会变得更麻烦。
(先花时间学一些CSS知识，会省去你以后大量的时间。)
7. table对对于页面布局来说，从语义上看是不正确的。
(它描述的是表现，而不是内容。)
8. table代码会让阅读者抓狂。
(不但无法利用CSS，而且会你不知所云)
9. table一旦设计完成就变成死的，很难通过CSS让它展现新的面貌。
(你看过CSS Zen Garden吗?)

从上面的原因可以看到，`table`不被推崇的主要原因是加载速度，影响用户体验。多重嵌套导致的卡顿。然而，一般正常的表格，都是比较少嵌套逻辑的，所以这种情况下使用绝对是百分百正确的！不过`table`标签用起来的确很不一样，所以务必贴些代码呐

```
.content-table{
  width: 100%;
  border-collapse: collapse; // 使table标签可以显示border边框
}

<table class="content-table table-contribute">
  <colgroup> // 来用批量控制每一列
    <col class="col-1">
    <col class="col-2">
    <col class="col-3">
    <col class="col-4">
    <col class="col-5">
  </colgroup>
  <tr>
    <th>题图</th>
    <th>标题</th>
    <th>投稿时间</th>
    <th>审核状态</th>
    <th>操作</th>
  </tr>
  <tr class="table-rows">
    <td>
      <img src="img/pc-editor-img-01.jpg" alt="" class="content-img">
    </td>
    <td>
      <p>谁是真大佬，不可思议迷宫万元现金选套路王</p>
    </td>
    <td>
      <p>2017-05-01 11:30:55</p>
    </td>
    <td>
      <p>未审核通过</p>
      <a class="table-check" href="javascript:void(0)">查看原因</a>
    </td>
    <td>
      <a class="table-editor" href="javascript:void(0)">编辑</a>
    </td>
  </tr>
</table>
```
