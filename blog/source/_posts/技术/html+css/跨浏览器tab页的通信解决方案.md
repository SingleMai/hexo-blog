---
title: 跨浏览器tab页的通信解决方案
date: 2017-10-11 18:34:10
categories: 'html+css'
---
这里有点类似于解决跨域方面的问题，如果你熟悉跨域，可能一切都会水到渠成。
<!-- more -->
将跨页面通讯类比计算机进程间的通讯，其实方法无外乎那么几种，而web领域可以实现的技术方案主要是类似于以下两种原理：
- 获取句柄，定向通讯
- 共享内存，结合轮询或者事件通知来完成业务逻辑

由于第二种原理更利于解耦业务逻辑，具体的实现方案比较多样。以下是具体的实现方案，简单介绍下，权当科普：

## 1.获取句柄
A页面中通过JavaScript的window.open打开B页面，或者B页面通过iframe嵌入至A页面，此种情形最简单，可以通过HTML5的 window.postMessage API完成通信，由于postMessage函数是绑定在 window 全局对象下，因此通信的页面中必须有一个页面（如A页面）可以获取另一个页面（如B页面）的window对象，这样才可以完成单向通信；B页面无需获取A页面的window对象，如果需要B页面对A页面的通信，只需要在B页面侦听message事件，获取事件中传递的source对象，该对象即为A页面window对象的引用：
```
a.html
const childPage = window.open('b.html', 'child');

childPage.onload = () => {
  childPage.postMessage('hello', location.origin);
}
/////////////////////////////////////
b.html
window.addEventListener('message', (e) => {
    let {data, source, origin} = e;
    source.postMessage('hell, parent', '/');
})
```
postMessage的第一个参数为消息实体，它是一个结构化对象，即可以通过“JSON.stringify和JSON.parse”函数还原的对象；第二个参数为消息发送范围选择器，设置为“/”意味着只发送消息给同源的页面，设置为“ * ”则发送全部页面。

### 提示
 1. 当指定`window.open`的第二个name参数时，再次用`window.open('****', 'child')`会使之前已经打开的同name子页面刷新
 2. 由于安全策略，异步请求之后再调用`window.open`会被浏览器阻止，不过可以通过句柄设置子页面的url即可实现类似效果
 ```
 // 在请求之前，首先先开一个空白页做准备
const tab = window.open('about:blank')

// 请求完成之后设置空白页的url
fetch(/* ajax */).then(() => {
   tab.location.href = '****'
})
 ```
### 优劣
缺点是只能与自己打开的页面完成通讯，应用面相对较窄；但优点是在跨域场景中依然可以使用该方案。

## 2.localStorage
两个打开的页面属于同源范畴。

若要实现两个互不相关的通源tab页面通信，可以使用一种比较巧妙的方式：localstorage。localStorage的存储遵循同源策略，因此同源的两个tab页面可以通过这种共享localStorage的方式实现通信，通过约定localStorage的某一个itemName，基于该key值的内容作为“共享硬盘”方式通信。

两者通讯的时机则通过h5新增的`storage`事件进行监听。
```
A.html
window.addEventListener('storage', (ev) => {
  if (ev.key == 'message') {
    // removeItem同样触发storage事件，此时ev.newValue为空
    if(!ev.newValue)
        return;
    var message = JSON.parse(ev.newValue);
    console.log(message);
  }
})
function sendMessage(message){
    localStorage.setItem('message',JSON.stringify(message));
    localStorage.removeItem('message');
}

// 发送消息给B页面
sendMessage('this is message from A');
///////////////////////////////
B.html 和A.html完全一致的写法
```
发送消息采用sendMessage函数，该函数序列化消息，设置为localStorage的message字段值后，删除该message字段。这样做的目的是不污染localStorage空间，但是会造成一个无伤大雅的反作用，即触发两次storage事件，因此我们在storage事件处理函数中做了if(!ev.newValue) return;判断。


### 提示
1. 当我们在A页面中执行sendMessage函数，其他同源页面会触发storage事件，而A页面却不会触发storage事件；

2. 而且连续发送两次相同的消息也只会触发一次storage事件，如果需要解决这种情况，可以在消息体体内加入时间戳：
```
sendMessage({
  data: 'hello world',
  timestamp: Date.now()
  });
  sendMessage({
    data: 'hello world',
    timestamp: Date.now()
    });  
```

3. safari隐身模式下无法设置localStorage值

### 优劣
API简单直观，兼容性好，除了跨域场景下需要配合其他方案，无其他缺点.
> ps: ie对storage事件的支持似乎不太好。IE10的storage事件会在页面document文档对象构建完成后触发，这在嵌套iframe的页面中造成诸多问题；IE11的storage Event对象却不区分oldValue和newValue值，它们始终存储更新后的值

## 3.WebSocket
这里不说了，又是它

## 4.BroadcastChannel、SharedWorker、Cookie
实现起来都没有特别的优点而且还稍微更不友好，所以不介绍，可以在原文查看

## 结合技术实现两个毫不相关的tab页通讯
这种情况才是最急需解决的问题，如何实现两个没有任何关系的tab页面通信，这需要一些技巧，而且需要有同时修改这两个tab页面的权限，否则根本不可能实现这两个tab页的能力。

这里其实思路很像利用iframe 实现出跨域。

在上述条件满足的情况下，我们就可以使用case1 和 case2的技术完成case 3的需求，这需要我们巧妙的结合HTML5 postMessage API 和 storage事件实现这两个毫无关系的tab页面的连通。为此，我想到了iframe，通过在这两个tab页嵌入同一个iframe页实现“桥接”，最终完成通信：
```
tab A => iframeA [bridge.html] => iframeB[bridge.html] => tabB
```
方向的通信原理如上图所示，tab A中嵌入iframe A，tab B中嵌入iframe B，这两个iframe引用相同的页面“bridge.html”。如果tab A发消息给tab B，首先tab A通过postMessage消息发送给iframe A（tab A可以获取到iframe A的window对象iframe.contentWindow）；此后iframe A通过storage消息完成与iframe B的通信（由于iframeA 与iframe B同源，因此case 2的通信方式这里可以使用）；最终，iframe B同样采用postMessage方式发送消息给tab B（在iframe中通过window.parent引用tab B的window对象）。至此，tab A的消息走通了所有链路，成功抵达tab B。

```
tab A:

// 向弹出的tab页面发送消息
window.sendMessageToTab = function(data){
    // 由于[#J_bridge]iframe页面的源文件在vstudio服务器中，因此postMessage发向“同源”
    document.querySelector('#J_bridge').contentWindow.postMessage(JSON.stringify(data),'/');
};

// 接收来自 [#J_bridge]iframe的tab消息
window.addEventListener('message',function(e){
    let {data,source,origin}  = e;
    if(!data)
        return;
    try{
        let info = JSON.parse(JSON.parse(data));
        if(info.type == 'BSays'){
           console.log('BSay:',info);
        }
    }catch(e){
    }
});

sendMessageToTab({
    type: 'ASays',
    data: 'hello world, B'
})
/////////////////////////
bridge.html

window.addEventListener("storage", function(ev){
    if (ev.key == 'message') {
        window.parent.postMessage(ev.newValue,'*');
    }
});

function message_broadcast(message){
    localStorage.setItem('message',JSON.stringify(message));
    localStorage.removeItem('message');
}

window.addEventListener('message',function(e){
    let {data,source,origin}  = e;
    // 接受到父文档的消息后，广播给其他的同源页面
    message_broadcast(data);
});
////////////////////////////
tab B

window.addEventListener('message',function(e){
    let {data,source,origin}  = e;
    if(!data)
        return;
    let info = JSON.parse(JSON.parse(data));
    if(info.type == 'ASays'){
        document.querySelector('#J_bridge').contentWindow.postMessage(JSON.stringify({
            type: 'BSays',
            data: 'hello world echo from B'
        }),'*');
    }
});

// tab B主动发送消息给tab A
document.querySelector('button').addEventListener('click',function(){
    document.querySelector('#J_bridge').contentWindow.postMessage(JSON.stringify({
        type: 'BSays',
        data: 'I am B'
    }),'*');
})
```

大概收录这么多，具体实现参考两篇文章：

-----------
参考：
[跨浏览器tab页的通信解决方案尝试](http://www.cnblogs.com/accordion/p/7535188.html)
[跨页面通信的各种姿势](https://zhuanlan.zhihu.com/p/29368435)
