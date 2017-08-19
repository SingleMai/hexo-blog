---
title: Ajax的实现（ajax与jsonp)
date: 2017-07-27 23:23:36
categories: 'js'
tags:
---
怎么抓取后端的数据？那必须是ajax，谈到ajax就有两个问题需要解决：1. 用何种数据进行交换？ 2. 跨域如何解决。只要稍微有所接触前端，都必须要知道ajax的实现和jsonp。这篇文章就大致理一遍两者的实现原理。

<!--more-->

## JSONP
### JSONP的产生背景与思考
1. 解决Ajax请求文件存在的跨域无权限问题。也就是安全性问题--跨域请求绝不允许
2. 当我们使用`<script>``<img>`等这些标签可以用`src`属性外联到各色网站的图片什么的。那种百度静态资源库就是这样啊。
3. `<script>`可以解决跨域问题，那我们用`<script>`标签去请求数据不就好了吗？就像你这样做。

```
// data.js
let data = {
    name: 'xixia',
    age: '19'
};

// index.html
<script src="data.js"></script>
```

4. 拿到数据之后，进行相应的渲染操作。——也就是回调函数，那这个功能也就实现了！

### 原理
接着背景继续讲。假设现在请求的就是一个js文件，那么如果在里面定义一个自执行函数。那不就相当于实现了*请求完成后处理数据*。
```
// data.js
let data = {
    name: 'xixia',
    age: '19'
};
(function callback (data) {
    alert(data.name);
})()
```

是不是渐渐有些眉目了，但是这个方法还是有问题。因为回调函数在服务器啊，前端咋整自己的骚操作呢？！很简单，我只要告诉服务器，我这边有一个回调函数，名字叫做‘callback’，我把名字告诉你，然后你返回数据的时候，加上这一句函数执行就好了。于是服务器的代码变成了这样。
```
// data.js
let data = {
    name: 'xixia',
    age: '19'
}
callback();
```
最后就是在前端定义这个函数就好啦，后端服务器不需要知道你捣鼓了啥，只需要知道前端小兄弟拿到数据之后就想捣鼓这个函数就行。

#### 源码
逻辑理通了，我们就直接贴原生js开干吧！为什么一定要原生呢？这不是废话吗，别人都封装好了，你哪里可以懂底层原理。这里贴的url地址是直接抓腾讯爸爸的接口，懒得填参数了，直接url里面都是他的参数了，大家都原理就行。填参都是拼接字符串的事。

后记，试着封装了一个插件。发现有一些需要注意的点：
- 插入的`script`记得在获取数据后删除。
- 最好是能对请求设置一个定时器，超过则返回错误。

```
    // 定义回调函数
    let GetRecomListCallback656586007521184 = function(e){
      console.log(e);
    }
    // 提供jsonp地址
    let url = 'https://c.y.qq.com/v8/fcg-bin/fcg_first_yqq.fcg?format=jsonp&tpl=v12&page=other&callback=GetRecomListCallback656586007521184&rnd=656586007521184&g_tk=5381&jsonpCallback=GetRecomListCallback656586007521184&loginUin=0&hostUin=0&format=jsonp&inCharset=utf8&outCharset=GB2312&notice=0&platform=yqq&needNewCode=0';
    // 创建script标签, 设置属性
    let script = document.createElement('script');
    script.setAttribute('src', url);
    // 把script标签加入head，此时调用
    document.getElementsByTagName('head')[0].appendChild(script);
```
#### 动手实现一个jsonp封装

#### 一个依赖`jsonp`这个插件的封装（转Promise对象）
```
import originJSONP from 'jsonp'

export default function jsonp(url, data, option) {
  url += (url.indexOf('?') < 0 ? '?' : '&') + param(data)
  return new Promise((resolve, reject) => {
    originJSONP(url, option, (err, data) => {
      if (!err) {
        resolve(data)
      } else {
        reject(err)
      }
    })
  })
}

function param(data) {
  let url = ''
  for (let i in data) {
    let value = data[i] !== undefined ? data[i] : ''
    url += `&${i}=${encodeURIComponent(value)}`
  }
  return url ? url.substring(1) : ''
}
```

## Ajax
异步请求，看名字就知道是请求。直接跑去W3C看这个[XMLHttpRequest](http://www.w3school.com.cn/xmldom/dom_http.asp)对象吧。
其实在[NO JQUERY，原生js操作DOM](https://singlemai.github.io/2017/05/15/%E6%8A%80%E6%9C%AF/js/NOJQUERY/)我已经说过怎么捣鼓了。这里稍微重复一下，毕竟还为了自己好找到写过的东西。

大致的步骤是：

1. 创建一个新的HTML请求`new XMLHttpRequest/window.ActiveXObject`
2. 设置请求类型和地址:`xmlhttp.open('POST','/url',true);`
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

#### 一个依赖`axios`的异步封装(转Promise对象)
```
export function getLyric(mid) {
  const url = '/api/lyric'

  const data = Object.assign({}, commonParams, {
    songmid: mid,
    pcachetime: +new Date(),
    platform: 'yqq',
    hostUin: 0,
    needNewCode: 0,
    g_tk: 67232076,
    format: 'json'
  })
  return axios.get(url, {
    params: data
  }).then((res) => {
    return Promise.resolve(res.data)
  })
}
```

#### 使用vue搭建起来的项目还可以修改host路径伪装实现欺骗性请求
主要是在`dev-server.js`这个文件里面搞事情
```
var axios = require('axios')
// 手动添加接口代理 axios
var apiRoutes = express.Router()
// 请求歌词
apiRoutes.get('/lyric', function (req, res) {
  let url = 'https://c.y.qq.com/lyric/fcgi-bin/fcg_query_lyric_new.fcg'
  axios.get(url, {
    headers: {
      referer: 'https://c.y.qq.com',
      host: 'c.y.qq.com'
    },
    params: req.query
  }).then((response) => {
    var ret = response.data
    if (typeof ret === 'string') {
      var reg = /^\w+\(({[^()]+})\)$/
      var matches = ret.match(reg)
      if (matches) {
        ret = JSON.parse(matches[1])
      }
    }
    res.json(ret)
  }).catch((err) => {
    console.log(err);
  })
})

app.use('/api',apiRoutes)
```

代码参考至：
[说说JSON和JSONP](http://www.cnblogs.com/dowinning/archive/2012/04/19/json-jsonp-jquery.html)
