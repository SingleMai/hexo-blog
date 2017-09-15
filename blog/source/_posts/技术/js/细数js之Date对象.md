---
title: 细数js之Date对象
date: 2017-08-05 14:43:03
categories: 'js'
tags:
  - '细数js'
---
细数JS系列之日期对象。
<!-- more -->
日期对象很简单，需要掌握的知识点比较少。
### 首先，创建一个当前日期
只要不传任何参数，就可以创建一个当前日期了。
```
var nowDate = new Date();
```

### 第二，创建指定日期
这个已经是用得比较少了吧。除了`Date.UTC()`方法可以传入参数外，还可以使用`Date.parse()`传入字符串，但是个人感觉`UTC`（格林威治时间）更好用吧，所以这里只介绍一种。
```
// GMT时间 2017年8月5日15时20分13秒
// 这个方法年月是必传的，其他参数如果没有则默认天数为1天，其它时间均为0
var date = new Date(Date.UTC(2017, 7, 5, 15, 20, 13));
```

### 第三，计算时间差
这里用到了ES5的`Date.now()`方法来创建当前日期。（因为这个方法返回的是毫秒数，可以很方便用于加减。当然，又是可爱的**ie9以下版本不支持**
```
// 取得开始时间
let start = Date.now();
// 调用函数
doSomeThing();
// 取得结束时间
let stop = Data.now(),
    result = stop - start;
```
所以有别的实现方式
```
// 取得开始时间
let start = +new Date();
// 调用函数
doSomeThing();
// 取得结束时间
let stop = +new Data,
    result = stop - start;
```
### 比较日期
和数值比较一样，直接比较即可。内部会调用`.valueOf()`方法返回毫秒数进行对比。
### 获取`Date`对象的日期时间
#### `.getTime()`和`.setTime()`
用好描述表示的日期。
#### `.getFullYear()` 和 `.setFullYear()`
获取以及设置年份，传入的年份必须是4位数
#### `.getMonth()` 和 `.setMonth()`
日期的月份，其中0表示1月，11表示十二月。设置日期的月份,如果超过11则增加年份
#### `.getDate()`和`.setDate()`
获取月份中的天数，传入1~31，如果超过该月应有的天数，则增加月份
#### `.getDay()` 和 `.setDay()`
获取日期中的星期几，其中0表示星期日，6表示星期六
#### `.getHours`和`.setHours()`
获取日期中的小时数，传入0~23，传入值超过23则增加天数
#### `.getMinutes` 和 `.setMinutes`
获取分钟，0~59
### `.getSeconds()`和`.setSeconds()`
依旧是0~59
### `getTimezoneOffset()`
该方法可返回格林威治时间和本地时间之间的时差，以分钟为单位。实际上，该函数告诉我们运行 JavaScript 代码的时区，以及指定的时间是否是夏令时。返回之所以以分钟计，而不是以小时计，原因是某些国家所占有的时区甚至不到一个小时的间隔。
```
var d = new Date();
console.log(d.getTimezoneOffset()); //-480（东八区，8*60）
```
