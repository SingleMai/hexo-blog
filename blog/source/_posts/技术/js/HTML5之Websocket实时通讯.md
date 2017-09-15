---
title: HTML5之Websocket实时通讯
date: 2017-09-13 20:03:06
categories: 'js'
tags:
---

想起来要学这个是因为在之后想要学习一波直播技术的实现，其中弹幕就用到了这个功能。那我们一起来看一下吧~~
<!-- more -->
## WebSocket是什么
WebSocket 是一种自然的全双工、双向、单套接字连接。使用WebSocket，你的HTTP 请求变成打开WebSocket 连接（WebSocket 或者WebSocket over TLS（TransportLayer Security，传输层安全性，原称“SSL”））的单一请求，并且重用从客户端到服务器以及服务器到客户端的同一连接。

这一堆定义一看就头晕，所以还是按照习惯地来简单地理解一下：WebSocket是一个HTML5新增协议，用来保持客户端和服务器的长时间同时（不断开），同时实现服务器向客户端推送信息。

先有大概这个认识，然后我们慢慢解决一些误区

### WebSocket与HTTP
是HTML5捣鼓出来的一个协议。用于解决HTTP无状态，不支持久连接的情况，但是与HTTP没有关系。

HTTP依旧是`1.1`和`1.0`两个，没有变化。之所以扯到HTTP是为了兼容浏览器的握手规范，所以可以把他理解为HTTP协议上的一个补充。（其实两者的层级不一样）

### WebSocket咋整
首先，Websocket是基于HTTP协议，（借用HTTP的协议来完成一部分握手），所以我们就从握手开始讲。
```
GET /chat HTTP/1.1
Host: server.example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: x3JJHMbDL1EzLkh9GBhXDw==
Sec-WebSocket-Protocol: chat, superchat
Sec-WebSocket-Version: 13
Origin: http://example.com
```
看习惯请求的应该就能发现，这里多了一些东西。一个一个来~~
```
Upgrade: websocket
Connection: Upgrade
```
这两行就是Websocket的核心，它告诉服务器，我需要的是Websocket，你去喊那个负责Websocket的小姐姐过来，不要喊HTTP那家伙来啦。
```
Sec-WebSocket-Key: x3JJHMbDL1EzLkh9GBhXDw==
Sec-WebSocket-Protocol: chat, superchat
Sec-WebSocket-Version: 13
```
至于这里，`Sec-WebSocket-key`是一个base64编码的值，是由浏览器随机生成的，告诉服务器：泥煤，不要忽悠我哦，我要验明正身，看你是不是websocket小妞妞。

之后，`Sec-WebSocket-Protocol`是一个用户自定义的字符串，用于区别同一URL下，不同的服务所需要的协议。简单来说：指明要哪个服务套餐。

最后`Sec-WebSocket-Version`则是告诉服务器，我要用第几号版本，也就是几号小妞妞来服务。

服务器看到这个请求之后就会识趣地去找websocket小妞妞过来，然后告诉你，这个请求已经被接受了，websocket小姐姐马上就来服务你了~
```
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: HSmrc0sMlYUkAGmm5OPpG2HaGWk=
Sec-WebSocket-Protocol: chat
```
这里，HTTP就有点像个老鸨一样，告诉你，我已经转化协议啦。websocket已经到咯~
```
Upgrade: websocket
Connection: Upgrade
```
然后的事情都是websocket的事儿了~~

## 为什么要用Websocket
### `long poll` 和 `ajax轮询` 为啥我觉得不行
在`websocket`没有来之前，遇到这种问题咋办呢？这里就介绍两种已经被淘汰状态的技术
#### Ajax轮询
ajax轮询原理十分简单，让客户端每隔几秒就跑去问一次服务器，有没有新进展。这就有点像，服务器就是炒菜的大厨，客户端就是端菜的小哥哥。
场景再现：
端菜小哥： 老大，你的菜炒好了吗
服务器： 没呢，你等下再来。
端菜小哥： 老大，你的菜现在炒好了吗
服务器： 没呢，你等下再来。
端菜小哥： 老大，你的菜还没炒好了吗
服务器： 尼玛，还问，你以为烫青菜啊？那么快，等下再来
端菜小哥： 老大，你的菜炒好了吧？
服务器： 两分钟前就炒好了，在那，去端走吧~

没错，这里狠心地把端菜小哥赖以生存的叮铃收起来了，而且小哥要到处端茶递水不允许他在厨房逗留。于是你就发现...端菜小哥马上就不玩辞职了

#### `long poll`
`long poll`其实原理上跟`ajax轮询`差不多，都是采用轮询的方式，但是采用的是阻塞模型。也就是说，客户端发起连接后，如果没有消息，就不返回Response给客户端，一直等到有消息才返回。客户端拿到返回后马上再建立连接。

同样是刚刚的小餐馆，老板娘觉得这种搞不行啊，伙计都跑了，上菜又慢。所以就把端菜小哥的任务细化了，他只需要端菜，不需要搞茶水了。
场景再现：
端菜小哥： 老大，你菜炒好了吗
服务器： 没，你在厨房门口站着等吧，哪都别去了
...于是端菜小哥就在厨房门口（服务器）发呆发愣数手指...
... 继续数手指...
..继续数..
服务器： 菜炒好啦，去上菜。
端菜小哥终于有事做了，拿着菜就跑去上菜，菜刚上完，跑回来厨房
端菜小哥： 老大，下一道菜好了没？
服务器： 站一边数手指去..等我通知

这样，问题是解决。但是老板娘却不开心了，毕竟她招了一个数手指的端菜小哥过来，白白浪费一个HTTP链接请求。

#### 缺点
于是，很容易看到上面两种情景都十分不行。HTTP的两个缺点——被动性。`ajax轮询`需要服务器有很快的处理速度和资源，假设有10个端菜小哥同时去问服务器拿菜，那服务器需要疯狂回答他们。（速度）`long poll`需要很高的并发能力，也就是接待客户的能力。（厨房足够的位置给小哥站着等）。所以两种方式都有可能出发`503 server unavailable（你拨的用户忙，请稍后再拨）`

于是两种方法，一种需要高速的处理速度，一种需要高并发能力。还有一个问题，HTTP的无状态性，服务器健忘，因为接待的人实在太多，所以你一走开，他就忘了你的东西。所以当你第二次去问服务器不能问：上次问你那道菜煮好没。而是要问“10号桌那道炒青菜可以了吗”

#### 解决
于是，老板娘为了解决这个问题，弄来了一个叮铃。端菜小哥把叮铃放在厨房窗口，让服务器有信息的时候就按铃，其余时间，他都去负责端水。于是乎，就解决了资源消耗方面的问题。这中间是怎么解决的呢?

其实我们所用的程序是需要经过两层代理的，即HTTP协议到Handler（node服务器）。代入场景就是，客户端（端菜小哥）、叮铃（websocket）、服务器（厨房炒菜大叔），没有叮铃的时候，HTTP小哥跑过来问。大家都知道声音传播速度很快，一下子就跑到厨房里了。但是厨房大叔炒菜要时间，所以当点菜的人多起来的时候，就会卡了很多单子。

引入叮铃这个工具后，小哥就可以省去跑过来问这段时间。当服务器把菜炒好了，直接按铃。同时，websocket可以让一直知道你的信息（在铃前放置排队的单子），那你就不用每次跑去都要问具体哪道菜可以了没。

## 怎么用~
这里使用起来并不十分复杂。有点值得一提的是，这里数据传输只允许用buffer和string，以为着传对象不可行。一开始，我居然懵了。后来发现自己傻逼了，明明只要在传之前`JSON.stringify()`然后再另一边解析回来就好了。。。。硬是自己没转过弯。 其余都是事件驱动型的操作啦，相信看源码你就能懂。

这里构建的是一个聊天室,当然由于时间不够，而且而是不要太复杂，避免demo的初衷，这里没有做过多的优化，只是简单粗暴实现了。关于客户端连地址那块地方需要根据自己的路径进行修改（为了和宿友通信，没有用`localhost`

-----
更新于2017.9.14
插播一条，weosocket可以解决跨域问题~~~文章写之前没有考虑到这个问题，所以导致现在布局不适合插入到任何地方。只能放这啦。可以解决跨域问题。可以解决跨域问题。可以解决跨域问题

### 服务器
```
var ws = require("nodejs-websocket");



let users = [];

console.log("开始建立连接...");
const server = ws.createServer(function(conn){
  conn.on("text", function (str) {
    console.log("收到的信息为:" + str);
    // 创建新用户操作
    if(isAddName(str)) {
      const name = str.slice(5, str.length);
      // 检测是否重名，用户创建是否成功
      if (isRepeatName(name)) {
        conn.sendText('登入失败，用户名已存在...');
      } else {
        users.push({
          name,
          connection: conn
        });
        sendTextToUser(`${name} 加入聊天室`);
      }
    } else {
      sendTextToUser(str);
    }
  })
  conn.on("close", function () {
    // 用户关闭页面的时候将信息清除
    for (let i = 0; i < users.length; i++) {
      if (conn === users[i].connection)
        users.splice(i, 1);
    }
    console.log("关闭连接")
  });
  conn.on("error", function (code, reason) {
    console.log("异常关闭");
  });
}).listen(8001);
console.log("WebSocket建立完毕");

function isAddName(str) {
  return str.includes('name:');
}
function isRepeatName(oriName){
  for (let i = 0 ; i < users.length; i++) {
    if (users[i].name === oriName)
      return true
  }
  return false;
}
function sendTextToUser(str){
  for(let i = 0; i < users.length; i++) {
    users[i].connection.sendText(str);
  }
}
```
### 客户端
```
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style>
        h1{margin: 0;}
        .chat-room{
          position: relative;
          left: 50%;
          margin-left: -450px;
          width: 900px;
          height: 600px;
          background: pink;
          overflow: hidden;
        }
        .header{
          display: flex;
          align-items: baseline;
          justify-content: space-between;
          box-sizing: border-box;
          padding: 0 40px;
          width: 100%;
          height: 80px;
          line-height: 80px;
          border-bottom: 2px solid grey;
        }
        .chat-romm .msg{
          height: 30px;
        }
        #name{
          height: 25px;
          text-indent: 1em;
        }
        .msg .btn-commit{
          display: inline-block;
          width: 60px;
          height: 100%;
          line-height: 30px;
          text-align: center;
          border-radius: 10px;
          color: #fff;
          background: #0997F7;
          text-decoration: none;
        }
        .msg .btn-commit:hover{
          background: #199EF8;
          transform: scale(1.05);
        }
        .msg .btn-exit{
          background: #F07676;
        }
        .msg .btn-exit:hover{
          background: #EB4848;
        }
        .chat-content{
          float:left;
          width: 75%;
          height: calc(100% - 80px);
          box-sizing: border-box;
          border-right: 2px solid grey;
        }
        .chat-content .msg-content{
          width: 100%;
          height: 75%;
          box-sizing: border-box;
          border-bottom: 2px solid grey;
          overflow-y: auto;
        }
        .chat-area{
          width: 100%;
          height: 25%;
        }
        .msg-list{
          list-style: none;
          font-size: 18px;
        }
        #chatting{
          width: 100%;
          height: 70%;
          font-size: 22px;
          box-sizing: border-box;
          padding: 5px;
          background: none;
          outline: none;
          border: none;
          resize: none;
        }
        .edit-area{
          display: flex;
          justify-content: flex-end;
          height: calc(100% - 70% - 5px);
        }
        .btn-send{
          display: inline-block;
          margin-right: 10px;
          width: 60px;
          height: 95%;
          text-align: center;
          line-height: 34px;
          text-decoration: none;
          color: #fff;
          background: #0997F7;
        }
        .chat-users{
          float: left;
        }
    </style>
</head>
<body>
<div class="chat-room">
  <div class="header">
    <h1 class="title">嘻嘻啊的聊天室</h1>
    <div class="msg">
      <input id="name" type="text" placeholder="请输入你的名字...">
      <a href="javascript:void(0)" class="btn-commit">确认</a>
    </div>
  </div>
  <div class="chat-content">
    <div class="msg-content">
      <ul class="msg-list">
        <!-- <li class="msg-item">
          <p class="msg-time">2017-9-13</p>
          <p class="content">谁谁谁：尼玛</p>
        </li>
        <li class="msg-item">
          <p class="msg-time">2017-9-13</p>
          <p class="content">谁谁谁：尼玛</p>
        </li> -->
      </ul>
    </div>
    <div class="chat-area">
      <textarea id="chatting" placeholder="请输入内容..."></textarea>
      <div class="edit-area">
        <a href="javascript:void(0)" class="btn-send">发送</a>
      </div>
    </div>
  </div>
  <div class="chat-users">
    <ul class="users-list">
      <!-- <li class="user-item">嘻嘻啊</li> -->
    </ul>
  </div>
</div>

<script>
    function getNowFormatDate() {
        var date = new Date();
        var seperator1 = "-";
        var seperator2 = ":";
        var month = date.getMonth() + 1;
        var strDate = date.getDate();
        if (month >= 1 && month <= 9) {
            month = "0" + month;
        }
        if (strDate >= 0 && strDate <= 9) {
            strDate = "0" + strDate;
        }
        var currentdate = date.getFullYear() + seperator1 + month + seperator1 + strDate
                + " " + date.getHours() + seperator2 + date.getMinutes()
                + seperator2 + date.getSeconds();
        return currentdate;
    }

    // var mess = document.getElementById("mess");
    // var content = document.getElementById("content");
    if(window.WebSocket){
        var ws = new WebSocket('ws://172.29.121.40:8001');

        ws.onopen = function(e){
            console.log("连接服务器成功");
            // ws.send("name:singlemai");
        }
        ws.onclose = function(e){
            console.log("服务器关闭");
        }
        ws.onerror = function(){
            alert("连接出错");
        }

        ws.onmessage = function(e){
          var li = document.createElement('li');
          li.innerText = e.data;
          li.classList.add('msg-item');
          msgList.appendChild(li);
        }

    }

    // 进入聊天室
    // 初始获得选择器
    var btnCommit = document.querySelectorAll('.btn-commit')[0];
    var btnSend = document.querySelectorAll('.btn-send')[0];
    var msgList = document.querySelectorAll('.msg-list')[0];
    var userList = document.querySelectorAll('.users-list')[0];


    btnCommit.addEventListener('click', function(){
      var name = document.querySelectorAll('#name')[0].value;
      if(name) {
        ws.send(`name:${name}`);
      }
    });

    btnSend.addEventListener('click', function() {
      var name = document.querySelectorAll('#name')[0].value;
      var content = document.querySelectorAll('#chatting')[0].value;
      if (name && content) {
        ws.send(`${getNowFormatDate()}<br>${name}:${content}`)
      }
    });
</script>
</body>
</html>
```

------
参考文章：
[Websockets 101](http://lucumr.pocoo.org/2012/9/24/websockets-101/)
[HTML5 WebSocket](http://www.runoob.com/html/html5-websocket.html)
[nodejs-websocket ](https://www.npmjs.com/package/nodejs-websocket)
[HTML5+NodeJs实现WebSocket即时通讯](http://www.cnblogs.com/axes/p/3586132.html)
[学习html5的WebSocket连接](http://www.cnblogs.com/shizhouyu/p/4975409.html)
