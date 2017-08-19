---
title: HTML&&CSS学习笔记
date: 2017-3-18 18:32:44
categories: "html+css"
tags:
     - html+css
---
在Udacity中学习时，关于HTML&CSS的笔记
<!-- more -->
# HTML&&CSS学习笔记
## 语义化元素的使用   
1. `<header>` 标签包括导航元素标签（<nav>）和标题元素标签；
1. `<article>`标签是可复用的结构标签。里面通常包含标题元素。
1. `<footer>`标签用来标示页尾，页面底端可以包含有关版权信息。
1. `<aside>`标签通常表示侧边栏或者插入的内容。
1. `<section>`表示文档，内容和主题，通常与标题组合。

1.
 ```
 <figure>
   <figcaption>黄浦江上的的卢浦大桥</figcaption>
   <img src="shanghai_lupu_bridge.jpg" width="350" height="234" />
 </figure>
```
## 响应式程序设计
* 设置窗口`<meta name="viewport" content="width=device-width, initial-scale=1.0">`
* 设置元素的最大宽度：`‘img,embed,object,video{max-width:100%;}’`
* 设置按钮大小：手指的大小是40px的大小。所以按钮的理想大小是48px
* 媒体查询:
  1. `@media screen and (min-width: 1px) and (max-width: 2px) `（小文件，大量HTTP请求）
  2.` <link rel="stylesheet" media="screen and (min-width:500px)" href="style.css">`（大文件，少请求）
* 弹性框架：flexbox
  1. display：flex
  2. flex-wrap：wrap
  3. order：number（初始值是0，如果设为-1，则是第一个显示）
* 常见的响应模式
  1. 掉落列模型（Column Drop）
  2. 大体流动模型（Mostly Floid）
  3. 布局切换器
  4. 画布之外（利用javascript）
    * 这里是用于切换 open 类的 JavaScript：
    ```
    menu.addEventListener('click', function(e) {
    drawer.classList.toggle('open');
    e.stopPropagation();
    });
    ```
    * 汉堡菜单相关css
        ```
        nav {
        width: 300px;
        position: absolute;
         /* This trasform moves the drawer off canvas. * /
        -webkit-transform: translate(-300px, 0);
        transform: translate(-300px, 0);
        /* Optionally, we animate the drawer. * /
        transition: transform 0.3s ease;
        }
        nav.open {
        -webkit-transform: translate(0, 0);
        transform: translate(0, 0);
        }
        ```
* [响应式表格](https://css-tricks.com/responsive-data-table-roundup/)：
  1. 隐藏列（hidden column）`display：none；`
  2. [没有更多的表格](https://css-tricks.com/responsive-data-table-roundup/)
  3. 表格内滚动：放在一个width=100%标签内再设置`overflow-x：auto`； [范例](http://codepen.io/JohnMav/pen/Mazrwm)
* 字体
  1. 每行的字符长度最好为：45-90个字符。网页上普遍是65个字符。
  2. `font-size：16px；line-height：1.2em；`
  3. 为溢出的内容[添加省略号](https://css-tricks.com/snippets/css/truncate-string-with-ellipsis/)
### 响应式图片
  1.` calc(（100% - 10px）/3)`的使用.加号和减号两边都要有空格。[详情](https://developer.mozilla.org/en-US/docs/Web/CSS/calc)
  2. 鲜为人知的css单位：
     1.`100vh`(viewport height):1vh代表1%的视图高度；
     2.`100vw`（viewpoint width）:1vw代表1%的视图宽度；
     3.`100vmin` &` 100vmax` 计算vh，vw中较大（小）的一个，把图片压缩为正方形；
  3. PageSpeed Insights(检查图片大小是否最优)
  4. 最好的图片使用就是不使用——用文本、[unicode字符集](https://unicode-table.com/en/sets/)
  5. [图标字体](http://weloveiconfonts.com/)
  6. 内嵌图片：`<img src="data:image/svg+xml;base64,[data]">`
  7. [SVG动画实例](http://codepen.io/chrisgannon/)
  8. 社交软件的导航LOGO [Zocial](http://zocial.smcllns.com/)
  9. 完全响应式
      1. [Srcset 与 Size](https://www.zfanw.com/blog/srcset-and-sizes.html#srcset_sizes) 添加在HTML元素内。
      Srcset格式为：`Srcset="src.jpg 100w/1x,src.jpg 200w/2x"`
      Size格式：`sizes="(max-width: 400px) 100vw, (min-width: 401px) 50vw"`
      2. 图片元素`<picture>`：
      ```
      <picture>
      <source media="(min-width:650px)"srcset="kitten-large.jpg">
      <source media="(min-width:450px)"srcset="kitten-medium.jpg">
      <img src="kitten-small.png" alt="Cute kitten">
      </picture>(注意载入的顺序）
      ```
      3. 在不支持`<picture> <srcset>`这些标签的浏览器中可以用[picturefill](http://scottjehl.github.io/picturefill/)来实现.

## CSS Reset
  1. 由于各个浏览器对css元素的解析效果不相同，这导致了不同客户使用不同浏览器的时候页面的解析不相同。所以css reset就是用来重置各个浏览器的默认值，保证网页解析到任何一个浏览器上都有相同的效果。
  1.

## CSS预处理
  1. “CSS 预处理器用一种专门的编程语言，进行 Web 页面样式设计，然后再编译成正常的 CSS 文件，以供项目使用。CSS 预处理器为 CSS 增加一些编程的特性，无需考虑浏览器的兼容性问题”

## 表格的使用
<table>
        <caption>Residents of the Mushroom Kingdom</caption>
		<!-- build table here -->
		<thead>
		<tr>
		    <th>Name</th>
		    <th>Address</th>
		    <th>Occupation</th>
		    <th>Salary</th>
	    </tr>
	    </thead>
	    <tbody>
	    <tr>
		    <td>Mario</td>
		    <td>514 Mushroom Kingdom</td>
		    <td>Plumber</td>
		    <td>49,150 Coins</td>
	    </tr>
	    		<tr>
		    <td>Luigi</td>
		    <td>6 Banshee Boardwalk</td>
		    <td>Plumber</td>
		    <td>45,115 Coins</td>
	    </tr>
	    <tr>
		    <td>Peach</td>
		    <td>123 Rainbow Road</td>
		    <td>Princess</td>
		    <td>215,675 Coins</td>
	    </tr>
	    <tr>
		    <td>Yoshi</td>
		    <td>120 Yoshi Valley</td>
		    <td> Wool Sales</td>
		    <td>67,980 Coins</td>
	    </tr>
	    <tr>
		    <td>Bowser</td>
		    <td> 91 Bowser Castle</td>
		    <td>  Real Estate Agent</td>
		    <td>180,779 Coins</td>
	    </tr>
	    </tbody>
	</table>

### 相应式表格——具体见响应程序设计
---
## 小知识点的归纳整理
1.	将div盒子居中：body 设置为 `text-align:center`; div设置为 `margin:0 auto`;
2.	占位图片的使用：http://placehold.it/1000x350 (狗)http://placepuppy.it/1000/350
3.	Web Font 服务英文组合型的有google.com/fonts 、中文有justfont
4.	语义化html更有实际意义
5.  line-height的应用：https://css-tricks.com/fun-line-height/

---
## 编写自动化图片压缩、优化工具
   1. [ImageMagick](http://www.imagemagick.org/script/index.php)
   2. [GraphicsMagick](http://www.graphicsmagick.org/)
   3. Grunt:
      1. [Grunt简介](http://gruntjs.com/getting-started)——[安装（中文）](http://m.blog.csdn.net/article/details?id=51789762)
      2. [Gunt使用入门](https://24ways.org/2013/grunt-is-not-weird-and-hard/)
      3. [用 Grunt 生成不同分辨率的图片](https://addyosmani.com/blog/generate-multi-resolution-images-for-srcset-with-grunt/)
      4. [用于生成多张图片的 grunt-responsive-images 插件](https://github.com/andismith/grunt-responsive-images)
      5. [用于响应式图片工作流的 grunt-respimg 插件](https://www.npmjs.com/package/grunt-respimg)
  4. 事例代码详见文件夹/.Web/html/关于图片自动化...

## 设置一个LocalHost
### 首先，必须打开 IIS：
“开始”>“控制面板”>“程序”>“打开或关闭 Windows 功能”（Start>Control Panel>Programs>Turn Windows features on or off）此时会显示“Windows 功能”方框。加载可能需要一些时间。向下滚动并找到“Internet 信息服务”，确保已勾选该方框。“Internet 信息服务”上方是“Internet Information Services 可承载的 Web 核心”，同样勾选该方框。
### 第二，必须打开“Internet Information Services (IIS) 管理器”并配置 IIS
单击“开始”按钮，然后在搜索栏中键入 IIS单击“Internet Information Services (IIS) 管理器”将其打开在左侧的“连接”窗格中，单击小箭头以打开结构树，然后右键单击“站点”文件夹单击“添加网站”
为站点指定一个名称，然后添加网站所在文件夹的路径。
转至 IP 地址，然后单击下拉列表。找到 IP 地址（例如 192.168.0.92）。（将其记在某处。我把它作为注释放在 html 和 css 文件中）选择一个端口。建议不要使用端口 80，但任何其他端口都可以。我使用的是 90单击“确定”。
### 第三，设置网站文件夹的读取权限
右键单击保存网站的文件夹，然后单击“属性”。
转至“安全”选项卡，然后滚动查看组名或用户名并查找以下内容：
IUSR 和 IIS_IUSRS
如果它们在该处，请确保已选中“读取并执行（read & execute）”和“读取（read）”。如果它们在该处但未选中，请单击其中一项，然后单击“编辑”。选中“读取并执行”和“读取”对应的方框。单击“应用”，然后单击“确定”。如果它们不在该处，请依次单击“编辑（edit）”和“添加（add）”。此时将显示“选择用户或组（Select Users or Groups）”方框。
单击“高级（Advanced）”按钮，然后单击“立即查找（Find Now）”按钮。向下滚动，直至找到 IIS_IUSRS。单击该项，然后按“确定（OK）”两次。单击“应用（apply）”。对 IUSR 重复以上操作
要在本地主机上显示网站
打开 Chrome 并键入 IP 地址和端口，如下所示：
192.168.0.142:90 (或者你的端口号)

# JavaScript基础
  详情请看JavaScript文档
