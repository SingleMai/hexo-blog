---
title: NO JQUERY，原生js操作DOM
date: 2017-05-15 22:44:29
categories: "js"
tags:
      - js
      - dom
---
每次面试或者和同学交流都会提到，原生js写DOM操作的重要性。虽然JQuery已经封装好了你所需要的一切。既然如此，为了熟悉js的操作，也是以后面试的需求。整理和记忆一下原生js操作DOM吧！
文章内容代码参考：[【第910期】No JQuery! 原生JavaScript操作DOM](http://mp.weixin.qq.com/s/XBOVVDrG727iv8zK174nDw)
<!-- more -->
## DOM操作：查找DOM
使用`.querySelector()`方法来查询DOM。需要传入任意的CSS选择器。
```
const myElement = document.querySelector('#foo > div.bar');
```
这行代码返回第一个匹配元素（深度优先）。相反，我们可以检查一个元素是否匹配一个选择器。
```
myElement.matches('div.bar') === true
```
如果我们想要获取所有的匹配元素，可以用：
```
var myElement = document.querySlectorAll('.bar');
```
如果我们已经得到了一个父类的引用，我们只查找它的子元素。那么可以这样使用提高性能：
```
var myChildElement = myElement.querySelector('input[type='submit']');
```
`.querySelector()`缺点：
  - 不是实时的，如果动态地添加一个匹配选择器的元素，使用这个方法不能找到该元素
  - 实时的元素集合不需要知道所有元素信息。而这个方法则会马上搜集所有信息到一个静态列表中，影响性能。

其他方法:`.getElementById`
## 元素列表
关于`.querySelector()`有两个大坑。
  1. 不能在结果上调用Node方法从而获得它的元素。
  2. 返回的结果是一个`Nodelist`，不是数组，不能直接调用数组的方法。
  ```
  Array.from(myElement).forEach(doSomething);
  ```
每个元素都有实时更新的只读属性：
```
myElement.children
myElement.firstElementchild
myElement.lastElementchild
myElement.previousElementsibling
myElement.nextElementsibling
```
因为Element接口继承Node接口，所以有以下属性：
```
myElement.childNodes
myElement.firstChild
myElement.lastChild
myElement.previousSibling
myElement.nextSibling
myElement.parentNode
myElement.parentElement
```
前一组属性只可以是元素节点，而后一组属性（除了`.parentElement`）的值可以是任何节点。比如文本节点。

## 修改class和属性
修改元素的class像下面代码一样简单：
```
myElement.classList.add('foo');
myElement.classList.remove('foo');
myElement.classList.toggle('foo');
```
元素的属性值可以这样得到：
```
var value = myElement.value;
// 设置属性
myElement.value = 'foo';
// 设置多条属性
Object.assign(myElement,{
  value:'foo',
  id:'bar'
  })
// 移除属性
myElement.value = null
```
注意还有`.getAttribute()`,`.setAttribute()`和`.removeAttribute()`这三个方法。这些方法直接修改元素的HTML属性（与DOM属性相对），因而会导致**浏览器重新渲染**所以会比只修改DOM属性代价更高。

作为一个小的原则，除非你真的想对HTML“持久化”那些改变，你就只用上面的方法修改与DOM属性不相关的HTML属性（比如colspan)(比如当克隆一个元素或者修改它的父元素的.innerHTML的时候想保持这些改变,参考第三部分).

## 添加CSS样式
添加CSS样式可以像其他属性一样设置，需要注意的是在Js中要写成驼峰式：
```
myElement.style.marginLeft = '2em'
```
如果我们想获得CSS规则的值，我们也可以通过`.style`属性。然而， 它只能拿到我们明确设置过的样式。想要拿到计算后的样式值，我们要用`window.getComputedStyle()`.他可以拿到这个元素并返回一个CSSStyleDeclaration。这个返回值包括了这个元素和继承自父元素的全部样式。
```
window.getComputedStyle(myElement).getPropertyValue('margin-left')
```
## 修改DOM
```
// 把element1作为element2的最后一个子元素插入
element1.appendChild(element2);
// element3之前，插入element2作为element1的子元素
element1.insertBefore(element2,element3);
```
如果我们不想移动元素，而是插入一个拷贝
```
myElementClone = myElement.cloneNode();
myParentElement.appendChild(myElementClone);
```
`.cloneNode()`可以传入一个布尔值，`true`表示深拷贝，会拷贝它的子元素。

创建元素节点或文本节点：
```
var myElement = document.createElement('div');
var myElement = document.createTextNode('some text');
```

然后我们可以像上面展示的代码那样插入创建的元素。如果我们想删除一个元素，我们不能直接删除，而要采用从它的父元素删除子元素的办法来实现，像这样：
```
myParentElement.removeChild(myElement);
```
这给了我们一个优雅的解决办法，也就是通过它的父元素间接删除一个元素：
```
myElement.parentNode.remove(myElement);
```

## 元素属性
每个元素都有`.innerHTML`和`.textContent`（还有`.innerText`和`.textContent`类似，但有重要的区别）它们分别表示HTML内容和纯文本内容。是可写属性。
```
myElement.innerHTML = "<div><span>new content</span></div>"
myElement.innerHTML = null
//添加子元素
myElement.innerHTML += <a href="">reading</a>
```
像上面的代码那样向HTML添加标记是通常是一个不好的注意，因为这样是丢失之前对影响元素的属性做的修改（除非我们把那些修改作为HTML属性而保留下来，参考第二部分）和已经绑定的事件监听。设置`.innerHTML`可以适合用在需要**完全丢弃原来的而替换成新的标记**的场景，比如服务端渲染。所以添加元素这样做比较好：
```
const link = document.createElement('a')
const text = document.createTextNode('continue reading...')
const hr = document.createElement('hr')

link.href = 'foo.html'
link.appendChild(text)
myElement.appendChild(link)
myElement.appendChild(hr)
```
但是这个办法会引起**两次浏览器的重新渲染**-每次添加元素都会渲染一次-而用设置.`innerHTML`的办法的话只会重新渲染一次。我们可以先把所有的节点组合在一个`DocumentFragment`里，然后把这一个片段添加到DOM里，这样可以解决这个性能问题。
```
const fragment = document.createDocumentFragment;
fragment.appendChild(link);
fragment.appendChild(text);
fragment.appendChild(hr);
myElement.appendChild(fragment);
```
## 事件监听
这应该是最常用的事件监听方法:
```
myElement.onclick = function onclick(e){
  console.log("click");
}
```
但是这是通常应该避免采用的方法。这里`.onclick`是一个元素的属性，也就是说你可以修改它，但是你不能用它再绑定其他的监听函数-你只能把新的函数赋给它，覆盖掉旧函数的引用。

我们可以用更加强大的`.addEventListener()`方法来尽情地添加各种类型的各种事件的监听器。它接受三个参数：事件类型（比如click），一个无论何时在这个绑定元素上该事件发生都会触发的函数（这个函数会得到一个事件对象传进去作为参数）和一个可选的配置参数，下面会更详细的解释。
```
myElement.addEventListener('click',function(event){
  console.log("click");
  })
myElement.addEventListener('click',function(event){
  console.log("again");
  })
```
在监听函数内部，`event.target`指向这个事件触发的元素（`this`也是，当然除非你用的是箭头函数。译者注：如果监听函数是箭头函数，里面的`this`指向的是`window`对象，如果是普通的`function`函数，里面的`this`指向的跟`event.target`相同，都是该元素本身）。因此你可以轻松的拿到它的属性：
```
const myForm = document.forms[0]
const myInputElements = myForm.querySelectorAll('input')

Array.from(myInputElements).forEach(el => {
  el.addEventListener('change', function (event) {
    console.log(event.target.value)
  })
})
```
## 阻止默认行为
注意在监听函数内部总是可以拿到`event`，但是当需要的时候明确地传入这个参数是一个好的实践（当然参数名称可以随意设置）（译者注：即使没有明确地给监听函数传入任何参数，在内部仍然可以拿到原生event对象，变量名就是event）。先不详细解释Event接口，一个特别需要注意的方法是`.preventDefault()`。它可以用来阻止浏览器的默认行为，比如跳转链接。另一个常见的应用场景是当前端的表单校验失败的时候，可以根据判断条件阻止表单提交。
```
myForm.addEventListener('submit',function(event){
  if (somenthing wrong){
    event.preventDefault();
  }
})
```
另一个重要的事件方法是`.stopPropagation()`，它可以阻止事件冒泡。也就是说在一个子元素上绑定了阻止事件冒泡的点击事件监听函数，而在它的某一个父元素上也监听了点击事件，在子元素上触发的点击事件，不会触发它的这个父元素的点击事件监听函数-否则，父子元素都会触发。

现在我们看一下`.addEventListener()`的可选的配置对象这个第三个参数，它可以有以下的布尔属性（它们的默认值都是`false`）：
- **capture**: 这个事件会先在父元素触发，然后再向下传递给它的子元素
- **once**: 这个属性表示这个事件只会被触发一次
- **passive**:  它的意思是`event.preventDefault()`会被忽略

事件监听可以用`.removeEventListener()`方法删除。它接受事件类型和回调函数的引用两个参数；例如，`once`选项也可以像这样实现：
```
myElement.addEventListener('change', function listener (event) {
  console.log(event.type + ' got triggered on ' + this)
  this.removeEventListener('change', listener)
})
```
## 事件委托
另一个有用的模式是事件委托：假如我们有一个表单，并且想给它的每一个`input`元素绑定一个`change`事件的监听函数。一种方法是上面已经介绍过的那样用`myForm.querySelectorAll('input')`取到所有的`input`元素，然后再通过遍历绑定事件。然而，我们其实只需要给表单本身绑定这个事件监听函数，然后检查`event.target`是否是`input`元素就可以了。
```
myForm.addEventListener('change', function (event) {
  const target = event.target
  if (target.matches('input')) {
    console.log(target.value)
  }
})
```
用这种模式的另一个优势就是它对**动态插入的子元素同样有效**，而不需要给每一个绑定新的监听函数。

## 动画
`window.requestAnimationFrame()`它接受一个回调函数作为参数。这个回调函数会接收到当前的时间戳作为参数：
```
const start = window.performance.now()
const duration = 2000

window.requestAnimationFrame(function fadeIn (now)) {
  const progress = now - start
  myElement.style.opacity = progress / duration

  if (progress < duration) {
    window.requestAnimationFrame(fadeIn)
  }
}
```
## 写你自己的帮助函数
确实，与jQuery简洁的链式的$('.foo').css({color: 'red'})表达式相比，总是要遍历元素去做什么可能是非常的繁琐。所以为什么我们不像下面这样写我们自己的快捷的方法呢？
```
const $ = function $ (selector, context = document) {
const elements = Array.from(context.querySelectorAll(selector))

  return {
    elements,

    html (newHtml) {
      this.elements.forEach(element => {
        element.innerHTML = newHtml
      })

      return this
    },

    css (newCss) {
      this.elements.forEach(element => {
        Object.assign(element.style, newCss)
      })

      return this
    },

    on (event, handler, options) {
      this.elements.forEach(element => {
        element.addEventListener(event, handler, options)
      })

      return this
    }

    // etc.
  }
}
```
因此我们有了一个没有向下兼容负担的只有我们需要的方法的超简洁的DOM库。尽管通常在元素的原型链上已经有了那些方法。这里有一个gist（更加详细深入一些），它展示了一些实现这些帮助函数的办法。我们还可以这样保持简单：
```
const $ = (selector, context = document) => context.querySelector(selector)
const $$ = (selector, context = document) => context.querySelectorAll(selector)

const html = (nodeList, newHtml) => {
  Array.from(nodeList).forEach(element => {
    element.innerHTML = newHtml
  })
}

// And so on...
```
## AJAX实现
AJAX相比JQuery实现要复杂得多，但是更容易体现出和服务器交互的过程。
大致的步骤是：
1. 创建一个新的HTML请求`new XMLHttpRequest/window.ActiveXObject`
2. 设置请求类型和地址:`xmlhttp.open('POST','/url',true)`;
3. 发出请求:`xmlhttp.send(postData)`;
4. 添加监听函数：`xmlhttp.onreadystatechange = function(){}`
    1. `xmlhttp.status`返回的请求头
    2. `xmlhttp.responseText`返回的数据
5. 在node.js服务器端
    1. `req.on('data',function(data){})`来获取数据。
    2. `res.json({})`来返回数据
```
var xmlhttp;
if(window.XMLHttpRequest){
    xmlhttp = new XMLHttpRequest();
}else if(window.ActiveXObject){
    xmlhttp = new window.ActiveXObject();
}else{alert("请升级至最新版本的浏览器");}
if(xmlhttp !=null){
    var postData = {"name": this.value};
    postData = (function(obj){ // 转成post需要的字符串.
        var str = "";
        for(var prop in obj){
            str += prop + "=" + obj[prop] +"&";
        }
        return str;
    })(postData);
    xmlhttp.open("POST","/url",true);
    xmlhttp.send(postData);
    xmlhttp.onreadystatechange=function(){
        if(xmlhttp.readyState==4&&xmlhttp.status==200){
          //如果返回的是200，则成功后操作
          //获得返回json信息
          var obj = JSON.parse(xmlhttp.responseText);
        }
    };
}
```

## DEOMO
[灯箱效果](http://codepen.io/SitePoint/pen/aJaggB/)
