---
title: 细数JS之JSON对象
date: 2017-9-17 22:19:44
categories: "js"
tags:
     - 细数js
---
面试时候被要求写出标准的JSON，然后我就嗯？嗯？！三个月前看的文章，啊好后悔没认真理完所有知识点。现在忘了...
<!-- more -->
几个问题，看看你理解JSON没：
- JSON究竟是什么东西？
- 为什么JSON易于数据交换？
- JSON和JS对象的区别？
- JS中`JSON.parse`、`JSON.stringify`和不常见的`toJSON`,这三个函数的参数和处理细节
## 什么是JSON
> JSON是一种数据格式，基于文本，优于轻量，用于交换数据

数据格式，也可以理解为数据表现的的标准形式。
- xml: `<person><name>小明</name><age>16</age></person>`
- url-search: `name="小明"&height="160cm"&weight="60kg"`
- JSON:`{"name": "小明", "height": 160, "weight": 60}`

基于文本，相对于二进制数据（10001010这样子的），所以我们常说“JSON字符串”

轻量级，即相同数据，以JSON的格式占据宽带更小

被广泛地用于数据交换，由于JSON易读，编写和机器解析。所以对于人和机器都是友好的。被广泛用于前后端交流：
`前端构造对象 => 转换成JSON字符串传递 => 后端接收 => 转化成后端的数据对象`

### JSON格式和JS对象
甚至在w3cschool网站上都列着 JSON是JS的子集。那这句话是什么意思呢？是不是正确的呢？我们来讲一讲前世今生。首先，JSON全名(javascript Object Notation),所以JSON的格式是基于JS的，
- 但是它是一种格式，而JS对象是一个实例，一个内存中的东西。
- JSON可以传输，因为基于文本。JS没办法传输
- 在语法上，JSON更严格，但是JS对象很宽松

| 对比内容 | JSON     | JS对象 |
| :------------- | :------------- | ---- |
| 键名       | 必须是加双引号       |可允许不加、加单引号、加双引号 |
| 属性值       | 只能是数值、字符串、布尔值和null<br/>也可以是数组或者符合JSON要求的对象<br/>不能是函数/NAN/undefined       |  都行 |
| 逗号问题 | 最后一个属性后面不能有逗号 | 可以 |
| 数值 | 前导不能为0，小数点后必须有数字 | 没限制 |


## `JSON.stringify()`和`JSON.parse()`的使用
JSON用于数据交换，所以进行数据传输时候总是需要将它转成字符串，或者字符串转回JS对象。

### `JSON.stringify(obj[, replacer [, space]])`
函数的签名就很明显可以看出，这个函数有两个可选参数。我们先看传入第一个参数：一个js对象。
```
var obj = {
  "name": "小明",
  "age": 19
};
JSON.stringify(obj); // {"name":"小明","age":19}
```
就是将js对象转换为字符串对象。——这里如果有开发经验会发现，传入的对象即使不是标准的也能顺利编译成功（有关这个函数的智能识别转化问题，后面谈）

### 第二个参数--过滤器
这是我个人的理解。可以在格式化对象的时候插入一些操作。
- 如果第二个参数是一个函数，那么序列化过程中的每个属性都会被这个函数转化和处理。注意这个函数必须要有返回值，函数接受两个参数，一个是键名，一个是属性值。如果传入的是数组，那么第一个是index，第二个是当前数组项。
- 如果第二个参数是一个数组，那么只有包含在这个数组中的属性才会被序列化到最终的JSON字符串中。
- 如果第二个参数是null，那作用上和空着没啥区别，但是不想设置第二个参数，只是想设置第三个参数的时候，就可以设置第二个参数为null。
- 这个参数在chrome浏览器下好像并没有实现好。如果传入的是数组对象，则会遍历成功，但如果传入的是对象，实现就会有问题。
```
var person = {
  "name": "xiaoming",
  "age": 19
};
JSON.stringify(person, ["name"]); // {"name":"xiaoming"}
JSON.stringify(person, function(key, item) {
  return item + 1
});
```

### 第三个参数用于美化输出——不使用
调用会插入参数的字符当作缩进符，这里不详细说。因为没有意义。序列化是为了传输，传输就是能越小越好，加莫名其妙的缩进符，解析困难（如果是字符串的话），也弱化了轻量化这个特点。。

### 影响`JSON.stringify`的神奇函数`toJSON`
如果你在一个JS对象上实现了toJSON方法，那么调用JSON.stringify去序列化这个JS对象时，JSON.stringify会把这个对象的toJSON方法返回的值作为参数去进行序列化。
```
var info = {
  "msg": "I LOVE U",
  "toJSON": function() {
    var replaceMsg = new Object();
    replaceMsg["msg"] = "Go Die";
    return replaceMsg;
  }
}
JSON.stringify(info); //"{"msg":"Go Die"}"
```
所以`Date类型`可以直接传给`JSON.stringify`做参数，就是`Date类型`内置了`toJSON`方法.
```
var data = new Date(); //Sun Sep 17 2017 16:55:31 GMT+0800 (中国标准时间)
JSON.stringify(data); // ""2017-09-17T08:55:31.007Z""
```

### 综上以及小tips
综上，对于后两个参数，第二个参数只使用数组来限定传入值。第三个参数不使用。是最好的选择。

那么我们再来说一下，这个函数如果传入的是不规范的JSON，他会耍什么小聪明——
- 键名不是双引号的（包括没有引号或者单引号），会自动变成双引号；字符串是单引号的，会自动变成双引号
- 最后一个属性最后有逗号的，会自动去掉
- 非数组对象的属性不能保证以特定顺序出现。（毕竟适用`for in`的时候也不能保证啊）
- 布尔值，数字，字符串的包装对象在序列化过程中会自动转换成对应的原始值。也就是说`new String('bala')`会变成`bala`。`new Number(2017)`会变成2017
- undefined、任意的函数（除了`toJSON`）以及`symbol`会出现以下情况
  - 出现在费数组对象的属性值中： 在序列化过程中会被忽略
  - 出现在数组中时： 被转换成null
- NaN/Infinity，不论在数组还是非数组的对象中，都会转化为null
- 所有symbol谓属性键的属性都会被忽略，即使在第二个参数中强制制定了。
- 不可枚举的属性会被忽略

## JOSN.parse(strObj[, reviver])
这是一个讲字符串JSON转化为js对象的方法。但是这个方法就没有智能化了，你需要传入一个符合标准格式的字符串JSON，不然会报错。所以构建字符串JSON的时候，尽量使用内置的转化方法，确保不会出错。

### 第二个参数，函数
这里有一个可选的第二个参数，这个参数必须是一个函数，这个函数作用在属性已经被解析但是还没返回前，将属性处理后再返回。还是直接看demo来得容易理解：
```
var obj = {
  "name": "xiaoming",
  "age": 19,
  "children": {
    "name": "xixi",
    "age": 5
  }
};
var strObj = JSON.stringify(obj);
JSON.parse(strObj, function(key, value){
  console.log(key, value);
  console.log('---');
  /* if(value === 'xixi')
    return undefined
  if(value === 5) {
    return 4
  }
  return value
  */
})
//////
name xiaoming
---
age 19
---
name xixi
---
age 5
---
children Object {}
---
Object {}
---
```
这里是一个深度优先的遍历。具体怎么深度优先遍历这里不详细说了，看demo应该能看懂，不懂百度一下吧

这里提两个注意点
- 如果 reviver 返回 undefined，则当前属性会从所属对象中删除，如果返回了其他值，则返回的值会成为当前属性新的属性值。在demo中因为没有设置返回值，所以最后只得到一个空对象
- 你可以注意到上面例子最后一组输出看上去没有key，其实这个key是一个空字符串，而最后的object是最后解析完成对象，因为到了最上层，已经没有真正的属性了。

## 最后的总结
JSON对象在这里大致解释完了。暂时这样吧~~周末愉快
