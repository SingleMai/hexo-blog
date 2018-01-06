---
title: javascript设计模式之发布订阅模式
date: 2017-08-04 23:06:43
categories: 'js'
tags:
  - 'javascript设计模式'
---
原本没这么快开始挖坑设计模式的233，但是在看vue源码博客的时候遇到了~而且也是之前学过，只是忘了。那么就开始吧！
<!-- more -->
发布订阅模式，稍微想一想就知道。其实我们一直有用到这个模式~因为js原生地就需要这个，而且必须用到
```
$box.addEventListener('click', function() {})
```
好处是显而易见的，事件驱动嘛~

## 1.怎么做
那么，怎么定制一个发布订阅模式呢？其实一开始如果没有学习，做起来真的感觉是一头雾水。然而学了之后就恍然大悟，大喊一句"so大斯捏"。没接触的各位，先停一停，自己思考一下怎么能实现这个功能。
- 通过一个"订阅"方法，订阅一个事件，在事件触发的时候执行一些操作
- 事件触发后发布事件
- 事件是一个抽象的概念，把它转换为满足某些条件的情况。（if语句)
- 这里是不是有点像回调函数的感觉。满足特定条件就执行回调
这样通俗分解的说法，有没有明显一点呢？那我们跟着分解的来。（这里的例子借鉴《JavaScript设计模式与开发实践》）
首先，小明来订阅销售楼事件。就和原生调用没啥区别~
```
salesMan.listen('100米的楼盘开售了', function(price) {
  console.log(`${price}元每平方米`);
})
```
好了，那么既然传递参数，那么我们来考虑一下怎么接受这些参数。既然回调，那么肯定要把传进来的函数先存起来，方便后面回调用。另外每个事件都是各自的函数回调，所以申明的必须是一个对象。
```
var salesMan = {};
salesMan.clientList = {};
salesMan.listen = function(key, fn) {
  if (!this.clientList[key]) { // 如果还没有订阅过这个事件
    this.clientList[key] = [];
  }
  this.clientList[key].push(fn);
}
```
好了，既然已经订阅了，那么就开始处理发布的东西。也就是回调的执行。
```
// balabala 好多条件判断下来 满足了事件触发
salesMan.trigger('100米的楼盘开售了', '100'); //发通告，发布事件出去
```
既然把事件发布了，那就肯定是回调开始啦~~
```
salesMan.trigger = function(key, ...arg) {
  var fns = this.clientList[key];
  // 没有人订阅过，直接返回
  if (!fns) {
    return false;
  }
  for (let i = 0; i < fns.length; i++) {
    fns[i].apply(this, arg);
  }
}
```
于是，这样子。一个简易版的发布订阅模型就搞定啦。但是还有很多细节要处理，例如，现在事件是针对一个特定对象的，如果有很多个对象需要监听，那么会耗费很多内存申明重复的逻辑代码，如何写成通用的方式呢。另外，它还污染全局。如何取消订阅捏~如何实现先发布再订阅呢~~

### 先发布再订阅
这里稍微提一下思路吧~~在当`trigger`的发布事件时候判断一下是否有人监听着。如果没有则储存起来，在`listen`触发的时候进行判断然后直接回调~~
这是大概思路吧~模仿着应该能做出来哒

## 2.发布订阅模式的优缺点
### 发布订阅模式的优点：
- 支持简单的广播通信，当对象状态发生改变时，会自动通知已经订阅过的对象。
- 发布者与订阅者耦合性降低，发布者只管发布一条消息出去，它不关心这条消息如何被订阅者使用，同时，订阅者只监听发布者的事件名，只要发布者的事件名不变，它不管发布者如何改变；同理卖家（发布者）它只需要将鞋子来货的这件事告诉订阅者(买家)，他不管买家到底买还是不买，还是买其他卖家的。只要鞋子到货了就通知订阅者即可。

对于第一点，我们日常工作中也经常使用到，比如我们的ajax请求，请求有成功(success)和失败(error)的回调函数，我们可以订阅ajax的success和error事件。我们并不关心对象在异步运行的状态，我们只关心success的时候或者error的时候我们要做点我们自己的事情就可以了~

### 发布订阅模式的缺点：
- 创建订阅者需要消耗一定的时间和内存。
- 虽然可以弱化对象之间的联系，如果过度使用的话，反而使代码不好理解及代码不好维护等等。

## 3.具体实现
最近在慢慢熟悉TS的编写方式，所以用TS写的哈~
```
class Observer {
  constructor(public value : any) {
    this.walk(value);
  }
  walk(obj : any) {
    var keys = Object.keys(obj);
    for (let i = 0; i < keys.length; i++) {
      this.convert(keys[i], obj[keys[i]]);
    }
  }
  convert(key : string, val : any) {
    defineReactive(this.value, key, val);
  }
}

function observe (value : any) {
  if (!value || typeof value !== 'object') {
    return
  }
  return new Observer(value)
}

function defineReactive(obj : any, key : string, val : any) {
  var property = Object.getOwnPropertyDescriptor(obj, key);
  if (property && property.configurable === false) {
    return
  }

  var getter = property && property.get;
  var setter = property && property.set;

  var childOb = observe(val);
  Object.defineProperty(obj, key, {
    enumerable: true,
    configurable: true,
    get: function reactiveGetter() {
      var value = getter ? getter.call(obj) : val;
      console.log(`访问：${key}`);
      return value
    },
    set: function reactiveSetter (newVal) {
      var value = getter? getter.call(obj) : val;
      if (newVal === value) {
        return
      }
      if (setter) {
        setter.call(obj, newVal)
      } else {
        val = newVal
      }
      childOb = observe(newVal)
      console.log(`更新：${key} = ${newVal}`)
    }
  })
}

let data = {
  user: {
    name: 'singlemai',
    age: 24
  }
}
observe(data);
console.log(data)
data.user.name = 'singlemai';

// var salesOffices = {};
// salesOffices.clientList = {};
// salesOffices.listen = function(key, fn) {
//   if (!this.clientList[key]) {
//     var arr = [];
//     arr.push(fn);
//     this.clientList[key] = arr;
//   } else {
//     this.clientList[key].push(fn);
//   }
// }
// salesOffices.trigger = function() {
//   var arr = this.clientList[arguments[0]];
//   console.log(this.clientList)
//   console.log(arguments[0])
//   for (var i = 0; i < arr.length; i++) {
//     var fn = arr[i];
//     fn.apply(this, arguments);
//   }
// }
// salesOffices.listen('abc', (price, square) => {
//   console.log(`price:${price} square=${square}`)
// })
// salesOffices.trigger('abc',200, 88);

class EventObj {
  clientList: Array<any>;

  constructor (){
    this.clientList = [];
  }

  listen(key : any, fn : any) {
    if (!this.clientList[key]) {
        this.clientList[key] = [];
      }
      this.clientList[key].push(fn);
  }
  trigger(key : any, ...arg: Array<any>) {
    let arr = this.clientList[key];
    for (var i = 0; i < arr.length; i++) {
        var fn = arr[i];
        fn.apply(this, arg);
      }
  }
  remove(key : any, fn : any) {
    const fns = this.clientList[key];
    if (!fns) {
      return false;
    }
    if (!fn) {
      fns.length = 0;
    } else {
      let newFns = fns.filter((item : any, index : number, arr : Array<any>) => {
        return item !== fn
      });
      this.clientList[key] = newFns;
    }
  }
}

var aa = new EventObj();
function bb(price : number){
  console.log(price);
}
aa.listen('100', bb);
aa.listen('200', function(price : number){
  console.log(price);
})
aa.trigger('100', 300);
aa.trigger('200', 350);
aa.remove('100', bb);
aa.trigger('100', 300);

```
