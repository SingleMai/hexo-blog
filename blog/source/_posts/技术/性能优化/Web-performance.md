---
title: 网站性能优化
date: 2017-3-15 10:32:44
categories: "网站性能优化"
tags:
     - 性能优化
---
在Udacity网站优化课程中学到的知识
<!-- more -->
# 网站性能优化
 1. Google 的 [PageSpeed Insights](https://developers.google.com/speed/pagespeed/insights/?url=www.baidu.com&tab=desktop) 会分析页面，并给出如何加快加载速度的相应建议。

## 关键渲染路径
  1. 浏览器进行的一系列步骤，进而将html、js等文件转换在屏幕上呈现像素内容。
  2. **优化关键渲染路径就是指最大限度缩短执行以下第 1 步至第 5 步耗费的总时间。**
  3. 步骤的总结
      1. 得到HTML文件，开始构建`DOM`,但是`DOM`的构建并不是马上完成的。
      2. 所以同步的，在`<head>`发现`css`和`JavaSctipt`链接，发送请求。
      3. 由于在获得CSSOM之前，我们没有办法利用样式表，所以要尽快创建。
      4. 在CSSOM创建完毕后，将会禁用JS所以要马上运行并在结束后继续完成DOM的构建。
      5. CSSOM→Render Tree→Layout→Paint
  4. 原则：先衡量再优化。
  5. [如何使用 Timeline 工具](https://developers.google.com/web/tools/chrome-devtools/evaluate-performance/timeline-tool?utm_source=dcc&utm_medium=redirect&utm_campaign=2016q3#saving-and-loading-recordings)
```@mermaid
  graph LR
  id1(DOM) --> id3(CSSOM)
  id1 --> id2(Javescript)
  id2 --> id1
  id2 --> id3
  id3 -->id4(Render Tree)
  id4 -->id5(Layout)
  id5 -->id6(Paint)
```
### `DOM`的产生
浏览器接受到`HTML`的响应请求，开始解析`HTML`文档。将字节转换为字符转换为令牌（StartTag & EndTag）再转换为树的节点。其中，令牌和结点是交叉进行的。一组令牌的产生就可以同步进行结点产生操作。
  - 节点包含了HTML元素的所有相关信息。
  - StartTag与EndTag 之间可能还会有结点（子结点）
  ```@mermaid
    graph LR
    id1(HTML response)-->id2(字节)
    id2 --> id3(字符)
    id3 --> id4(令牌)
    id4 --> id5(结点)
    id5 --> id6(DomTree)
```
### `CSSOM`的产生
`CSSOM`的产生方式与HTML的产生方式差不多。有几个小点有所区别。
  - CSS判断结点的方式不是`<>`而是内置的一套结点方式。然后根据用户重写的部分，重置一系列的属性。也就是说，`CSSOM`一定程度上和层叠规则相关。
  - 所以这也就导致了它不能了`DOM`一样，利用一部分的令牌提前生成结点，而必须等到所有令牌产生完毕才可以生成。
  ```@mermaid
    graph LR
    id1(link到CSS样式表)-->id2(字节)
    id2 --> id3(字符)
    id3 --> id4(令牌)
    id4 --> id5(结点)
    id5 --> id6(CSSOM)
```
### `Render Tree`
1. 从 DOM 树的根节点开始遍历每个可见节点。
    - 某些节点不可见（例如脚本标记、元标记等），因为它们不会体现在渲染输出中，所以会被忽略。
    - 某些节点通过 CSS 隐藏，因此在渲染树中也会被忽略，例如，上例中的`<span>`节点---不会出现在渲染树中，---因为有一个显式规则在该节点上设置了`display: none`属性。
2. 对于每个可见节点，为其找到适配的 CSSOM 规则并应用它们。
3. 发射可见节点，连同其内容和计算的样式。
*注：简单提一句，请注意 `visibility: hidden` 与 `display: none` 是不一样的。前者隐藏元素，但元素仍占据着布局空间（即将其渲染成一个空框），而后者 (`display: none`) 将元素从渲染树中完全移除，元素既不可见，也不是布局的组成部分。*

[渲染树构建、布局及绘制](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/render-tree-construction)
### `Layout`
`<meta name="viewport" content="width=device-width">`这条语句就是将浏览器的布局窗口的宽度设置为设备的屏幕宽度。如果没有这条语句，浏览器会自动设置为980px；
1. 为弄清每个对象在网页上的确切大小和位置，浏览器从渲染树的根节点开始进行遍历。布局流程的输出是一个“盒模型”，它会精确地捕获每个元素在视口内的确切位置和尺寸：所有相对测量值都转换为屏幕上的绝对像素。

### `Paint`
最后，既然我们知道了哪些节点可见、它们的计算样式以及几何信息，我们终于可以将这些信息传递给最后一个阶段：将渲染树中的每个节点转换成屏幕上的实际像素。这一步通常称为“绘制”或“栅格化”。
*注意：绘制也有不用的时间。单色的绘制最为简单，相反阴影的绘制花费时间却要大得多*

## 优化关键渲染路径
- 三个可变因素
  - 关键资源的数量。（优化css、js）
  - 关键路径长度。（http请求次数）
  - 关键字节的数量。（文件大小）
- 优化常规步骤
  - 对关键路径进行分析和特性描述：资源数、字节数、长度。
  - 最大限度减少关键资源的数量：删除它们，延迟它们的下载，将它们标记为异步等。
  - 优化关键字节数以缩短下载时间（往返次数）。
  - 优化其余关键资源的加载顺序：您需要尽早下载所有关键资产，以缩短关键路径长度。
- [查看关键渲染路径性能模式](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/analyzing-crp#performance-patterns)
- [ Ilya 的著作 高性能浏览器联网](https://hpbn.co/)
- [浏览器预加载程序如何加快页面加载速度](https://andydavies.me/blog/2013/10/22/how-the-browser-pre-loader-makes-pages-load-faster/)https://andydavies.me/blog/2013/10/22/how-the-browser-pre-loader-makes-pages-load-faster/
### 优化DOM
  1. 尽快地将HTML流式传输给客户端，使浏览器可以开始构建DOM。
  2. 去除注释
  3. 利用GZIP压缩文件
  4. 确保浏览器缓存了这些文件[http缓存](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching)
  [详情](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/optimize-encoding-and-transfer#minification-preprocessing--context-specific-optimizations)
### 优化CSS
  除了和优化DOM出不多的优化方法外，还需设置取消阻塞渲染。默认情况下，CSS 被视为阻塞渲染的资源，这意味着浏览器将不会渲染任何已处理的内容，直至 CSSOM 构建完毕。请务必精简您的 CSS，尽快提供它，并利用媒体类型和查询来解除对渲染的阻塞。或者内嵌css

  **HTML 和 CSS 都是阻塞渲染的资源。** HTML 显然是必需的，因为如果没有 DOM，我们就没有可渲染的内容，但 CSS的必要性可能就不太明显。例如一些对于响应式用户界面专用设置的css在不符合情况下就没有必要进行阻塞渲染。将这些内容转移到另外文件可以使阻塞渲染的css文件内容更少，下载速度更短。
  - 常用的媒体查询取消阻塞渲染。`media ="print/(min-width: 40em)/orientation:portrait"`
  *请注意“阻塞渲染”仅是指浏览器是否需要暂停网页的首次渲染，直至该资源准备就绪。无论哪一种情况，浏览器仍会下载 CSS 资产，只不过不阻塞渲染的资源优先级较低罢了。*

### 优化JavaSctipt
  1. 详情看[使用 JavaScript 添加交互](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/adding-interactivity-with-javascript)
  2. 每一次执行到Js脚本，浏览器的解析就会停止，直到脚本运行完毕。脚本的运行可以影响HTML也可以影响CSS，这在修改CSS样式的时候很可能会造成大量的延迟。因为要修改CSS、可是CSS在CSSOM没完成前不能工作，而CSSOM在DOM没完成前不能工作。这又导致脚本将延迟执行。大量的关系依赖，会严重影响性能。也就是CSS会阻止脚本的执行。所以优化CSS显得很重要。
  3. `async`属性，在头部可以让JavaScript不停止解析器的进行。但是不能内嵌在html使用。内嵌的JavaScript始终会让CSS阻止
  4. 添加事件监听在页面解析完，发出Onload指令时，再进行操作。
  5. 此外，你还可以向脚本标记添加 defer 属性，告诉解析程序脚本应该等到文档加载后执行。[详细了解 defer 属性](https://hacks.mozilla.org/2009/06/defer/)
      - 阻止（Blocking）: <script src="anExteralScript.js"></script>
      - 内联（Inline）: <script>document.write("this is an inline script")</script>
      - 异步（Async）: <script async src="anExternalScript.js"></script>

# 浏览器渲染优化
  1. 帧
  ```@mermaid
    graph LR
    id1(JavaScript或CSS动画或API)-->id2(style)
    id2 --> id3(Layout)
    id3 --> id4(Paint)
    id4 --> id5(composite)
  ```
  - 不同的操作会不同地影响帧的部分。
    - 通过CSS或JavaScript对外观做出了变化，那么浏览器必须重新计算受影响的样式。如果修改的是布局元素，那么则需要重新布局。然后重新绘制修改的部分，再合成。
    - 如果只修改样式，外观。那么则不需要重新布局
    - 只需要JS->style->composite合成——浏览器将网页的单个图层合并到一起。
    - 如果是float布局，那么页面的缩放可能会影响只出现Layout->paint->composite
  2. [csstrigers.com](https://csstriggers.com/)

## App生命周期
**RAIL网络应用生命周期的四大领域**：响应、动画、闲置、加载
1. 加载即关键路径渲染：需要在1秒内完成
2. 在加载完进入页面后，页面会出现闲置状态。即用户没有操作，这时候可以处理一些繁杂的任务的机会。以便让用户操作的时候一切都能顺利进行。（50毫秒时间）
3. 动画12毫秒时间
    - “初始、结束、倒转、播放”（FLIP）技巧；利用浏览器已经生成的内容，通过移动转化简化动画的动作。在退出利用已经产生的动画往后播放。[源代码](https://github.com/udacity/devsummit)[讲话](https://speakerdeck.com/paullewis/making-a-silky-smooth-web)
4. 响应时间：100毫秒

## JavaSctipt
1. 尽量不要采用微操作去修改代码。因为JavaSctipt实际运行代码的方式和我们的编写方式是不一样的。即，我们不知道浏览器会去怎样理解代码执行出来。所以用替换代码的方式可能往往不奏效。
2. `requestAnimationFrame()`是制作动画的函数。可以告诉浏览器执行动画的顺序而不必因为突然产生需要处理的脚本而导致渲染管道的重新设置。
    尽管JQ扔使用`setTimeout（）`来实现动画。`setTimeout()`在执行的时候不会关注管道渲染。如果需要执行重复任务，或者等待一段时间的时候可用。
  ```
  function animate(){
    //Do something
    requestAnimationFrame(animate);//执行下一个动画
  }
  requestAnimationFrame(animate);//执行第一个动画
  ```
 3. [Web Work](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Workers_API/Using_web_workers)
  [basic of Web Work](https://www.html5rocks.com/en/tutorials/workers/basics/)
 4. [编写高效且节省内存的JavaSctipt](https://www.smashingmagazine.com/2012/11/writing-fast-memory-efficient-javascript/)
 5. [内存管理](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Memory_Management)
 6. [Build New Games 上的高性能垃圾收集器友好代码](http://buildnewgames.com/garbage-collector-friendly-code/)

## 样式和布局
 1. [Block Element Modifier](https://www.sitepoint.com/bem-smacss-advice-from-developers/)[methodologyKey concepts](https://en.bem.info/methodology/key-concepts/)

2. 这里是我用来替换 `document.querySelectorAll` 的辅助函数。它会创建 DOM 节点数组，我认为这很有用，因为` forEach()` 等数组方法非常实用。
  ```
  function getDomNodeArray(selector) {
    // get the elements as a DOM collection
    var elemCollection = document.querySelectorAll(selector);

    // coerce the DOM collection into an array
    var elemArray = Array.prototype.slice.apply(elemCollection);

    return elemArray;
  };

  var divs = getDomNodeArray('div');
  ```
 强制同步布局是影响性能的重要原因。[影响布局的CSS元素](http://gent.ilcore.com/2011/03/how-not-to-trigger-layout-in-webkit.html)

## 合成和绘制
### 创建一个新图层
1. `will change:transform;`可以给浏览器一个暗示，我们即将在某个时间点更改元素transform。暗示浏览器新建一个图层。但是浏览器会自己决定是否要创建。
2. `transform:translateZ(0)`：强制浏览器新建一个图层。
3. 创建图层可以明显优化那些只需要移动而不需要修改样式的元素。利用新建的图层，只需要经过合成这一步即可完成。
4. 可以为背景创建图层
5. 在Chrome开发者工具按`ESC`调出面板添加`render`打开Paint 和 Layout


## 未完成或待研究项目
1. 二维码应用
    - 克隆版本库qrcode
    - 安装 npm
    - 安装 Gulp
    - 在二维码应用目录中运行 npm install
    - 通过 gulp serve 构建并运行
2. pizza
3. new aggreator
4. web worker demo --- 对图片进行修改
