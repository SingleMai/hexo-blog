---
title: 有关decodeURI与unescape解码问题
date: 2018-08-19 10:24:59
categories: 'js'
tags:
---
这是很久之前为公司做的一个白板服务有关解码问题的总结。情况比较特殊吧，每个模块输出信息可能都没对齐，然后放到node来总结。加上对二进制操作比较粗糙，所以此文纯粹说明一下我的坑，不一定能解决大家的问题。
<!-- more -->

在开发过程中,后台发送过来的数据内容被使用URL进行编码.所以前端拿到数据后需要进行遍历转码.一开始使用`decodeURI`发现,居然报了一个错误:`malformed URI sequence , URI不合法`.

当时时间比较紧,加上自身对解码这一块可谓零接触.所以就开始忙乎着复制下一个方法`unescape`进行解码.一试,哎哟,居然可以.然后这个问题就过了.直到最近发现转完的内容还是有格式错乱的问题.一开始定位为后端的问题,但是大家都把自己拿到的内容进行转码.最终定位到是我前端的问题.这下我就慌了,立马百度一下`escape`这个方法,发现,卧槽居然是一个老早就被弃用的方法.尖介,可是明明推荐使用的是`decodeURIComponent`,但是却报错阿喂.查了一圈过来,就发现原来是因为后台进行转码的时候使用的过时的转码方法(同样是被弃用了),于是乎你就懂了

后台用过时的方法转码 --> 前端用过时的方法`unescape`解码
后台用现在推荐的方法转码 --> 前端用推荐的方法`decodeURIComponent`解码

而且还必须一一对应,不然会报错.(这是一个好事情啊,这样就能catch住这个错误,转向另一种解法了)

```js
const util = {}

util.decode_JSON_URI = function (obj) {
  for (const item in obj) {
    const value = obj[item];
    if (typeof(value) === 'string') {
      obj[item] = decodeURIComponentEx(value);
    }
    if (value instanceof Object) {
      obj[item] = util.decode_JSON_URI(obj[item]);
    }
  }
  return obj;
}

exports = module.exports = util


var decodeURIComponentEx = function (uriComponent) {
  if (!uriComponent) {
    return uriComponent;
  }
  var ret;
  try {
    ret = decodeURIComponent(uriComponent);
  } catch (ex) {
    ret = unescape(uriComponent);
  }
  return ret;
};  
```

后记，时隔好久好久，又出现了另外一个问题。使用`decodeURIComponent`解码会导致报错，紧接着使用`unescape`进行解码就会导致中文格式乱码。

本来想把锅甩给后端，结果后台拿着他传给我的字段跑到网上的在线转码工具一解码，我勒个去完全没问题。。然后就变成我的锅了。。可是网上找来找去都没搞过这种骚操作的。绝望之余，开始在github上寻求基友的帮助，然而好坑的是几乎每一个所谓的框架里面都是一句

```js
  try {
    ret = decodeURIComponent(uriComponent);
  } catch (ex) {
    return -1
  }
```

尼玛，比我还坑好吗。好歹搞一个`unescape`啊。。。绝望之余看到一个`jquery-url-decode`什么的，感觉也没报多大希望，纯属是对jquery大佬的敬畏点了进去。咦，发现他居然是直接对二进制进行修改封装？纳尼？这么强势，直接就复制下来开始刚

```js
	function utf8_decode(utftext) { 
		var string = ""; 
		var i = 0; 
		var c = 0;
		var c2 = 0; 
 
		while ( i < utftext.length ) { 
 
			c = utftext.charCodeAt(i); 
 
			if (c < 128) { 
				string += String.fromCharCode(c); 
				i++; 
			} 
			else if((c > 191) && (c < 224)) { 
				c2 = utftext.charCodeAt(i+1); 
				string += String.fromCharCode(((c & 31) << 6) | (c2 & 63)); 
				i += 2; 
			} 
			else { 
				c2 = utftext.charCodeAt(i+1); 
				c3 = utftext.charCodeAt(i+2); 
				string += String.fromCharCode(((c & 15) << 12) | ((c2 & 63) << 6) | (c3 & 63)); 
				i += 3; 
			} 
 
		} 
 
		return string; 
	}
	// 调用
	utf8_decode(unescape(a.replace(/\+/g, ' ')))
```

我了个乖乖，这么强势的操作，必须看一下行不行。一试纳尼居然成功了！之后虽然导致了别的位置乱码，但是好歹把这个问题找出来了。但是由于把这个操作没办法报错，和`unescape()`没办法区分开到底使用哪一个。所以只能改成这样

```js
  try {
    ret = decodeURIComponent(uriComponent);
  } catch (ex) {
    ret = utf8_decode(unescape(uriComponent.replace(/\+/g, ' ')))
  }
```

嗯，不知道有没Bug呢 暂时就这样呗