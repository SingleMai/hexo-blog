---
title: ES6中的arguments对象和parameters对象
date: 2017-06-29 22:08:06
categories: 'js'
tags:
---

arguments 是是JavaScript里的一个内置对象，它很古怪，也经常被人所忽视，但实际上是很重要的。所有主要的js函数库都利用了arguments对象。所以agruments对象对于javascript程序员来说是必需熟悉的。也是很容易从中那种设计模式的书中看到代码对其使用。

所以自然就想着深入探讨一下啦~~~查文档的时候发现还有一个parameters对象。那就干脆混为一谈啦~
<!--more-->
## 实参和形参
`arguments`和`parameters`经常被混为一谈，但是其实两者还是有区分的。在大多数标准中，`parameters`是我们定义函数时设置的名字（形参），`arguments`（或者是）实参是我们传入函数的参数。举个栗子，轻松理解~~
```
function foo(param1, param2){
    // do something
}
foo(10, 20);
```
在这个函数里，`param1`和`param2`是函数的形参。而传入的函数值10，20 则是实参。

## arguments对象
arguments对象在高版本的ES下其实已经不被支持了。但是它的存在却是很有意义的。而且虽然ES不对其进行支持但也提供了另外的简便方法来实现。

arguments对象更多是为了可以传给函数不确定数目参数而存在的。例如，我们想要实现，一个`add()`函数，将传入的所有实参都相加然后返回。但是有一个问题，就是我们不知道用户将会传入多少个参数，这种情况下只能要求用户传入的必须是一个数组，然后我们再对数组进行分解。然而，如果场景变得复杂起来，那么没有重载的JavaScript将会十分困难进行实现。

由于arguments已经不被建议使用，那么这里探讨它的使用已经变得没有意义啦。所以我们来探讨一下ES6支持的新特性。

至于不被建议的原因，我大概除了语法上那些我也没太弄清楚的原因。还有一个就是调用的时候我们为了知道参数的意义，往往需要去查看源码。在大型项目中，这显得特别低效率。同时也可以得出一个结论，函数的形参是十分有必要存在的。arguments带来的[性能问题](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers#3-managing-arguments)

## 扩展运算符`...`
扩展运算符其实很简单，举个栗子大家就懂了。例如将一个数组拆分为单独的值传入函数。
```
let arr1 = [1,2,3];
function print (){
    for(let i in arguments){
        console.log(arguments[i])
    }
}
print(...arr1)//输出 1  2  3
```
扩展运算符可以在构造函数中方便的使用。
```
new Date(...[2016,6,29]);
```
而且还支持多次使用。
```
print(...arr1, 4, ...[5,6]) //调用上面的方法。输出 1 2 3 4 5 6
```

## 剩余参数reset
rest参数的语法和展开运算符一样，但是使用的位置不同，作用也是相反：展开运算符是展示数组的每一项变为参数，rest参数是把多个参数变为一个数组。同样是一个栗子
```
function turnToArr(...arr1){
    return arr1
}
turnToArr(); // [] 不传参的情况下会变成空数组
```
。rest参数在创建可变参数函数是尤其有用（一个函数可以接收多个、不固定的参数）。自然而然，reset参数就是一个可以很好替代arguments对象的东东。

使用rest参数来替代arguments提高了代码的阅读性，而且避免了一些由于。当然rest参数也有局限性，它必须定义为最后的参数，否则会产生语法错误。

* reset参数还是一个数组，可以有length属性

但是reset参数也有局限性。例如：
* 它必须最定义为最后的参数，否则会产生语法错误。
* 只能在函数里定义一次，也就是只能使用一次。

## ES6中的默认值
在ES6里，我们不需要检测参数是否为`undefined`，然后来模拟默认值。我们在函数定义时就可以使用默认值。
```
function foo(a = 10, b = 10){
    console.log(a, b);
}
foo(5); // 5 10
foo(0, null); // 0 null
```
没传值时，参数会被设置为默认值。即使传入0和null都不会触发默认值。我们甚至可以把函数作为默认值设置到函数的定义里。

注意：和别的语言不一样，JS执行默认值在执行时。
```
function add(value, array = []) {
  array.push(value);
  return array;
}
add(5);    // [5]
add(6);    // [6], not [5, 6]
```
上面的代码意思大概是，默认值在退出函数后就会被销毁。

## 结构赋值
结构赋值是ES6的一个新特性，通过它你可以把数组、对象的每一项变为形参，类似对象和数组的迭代器。
举个很简单的栗子，马上知道怎么用！！
ES5状态下，这么写：
```
function initiateTransfer(options) {
    var  protocol = options.protocol,
        port = options.port,
}
options = {
  protocol: 'http',
  port: 800,
};
initiateTransfer(options);
```
ES6状态下，你可以这样：
```
function initiateTransfer({protocol,port} = {}){
    // code to initiate tranfer
}
initiateTransfer(options);
```
使用了结构赋值语法，在调用函数时就必须要赋值，但是如果我们想让这个参数变为可选项怎么办？为了方式没有传参是抛出的错误，所以推荐使用默认值语法（即让他没传值的时候默认为空对象）。

但如果我想给每一项都设置一个默认值，那也是杠杠的阔以。就是写起来怪怪的，有点头重脚轻，但是确实十分的便利。
```
function initiateTransfer({
    protocol = 'http',
    port = 800,
})
```

## 传参
JS只有按值传参，JS只有按值传参，JS只有按值传参。如果传入的是一个指向对象的引用，那么传进去的是这个对象引用的地址（`值`）

## 类型检测，参数的缺失和多余传入
由于JS是不检测传入参数的类型以及个数的。所以，当我们有一个函数，我们只希望它接收一个参数的时候，我们没办法进行限制。调用时候甚至不传参都不报错。

实参和形参的数量可以这样比较
* 实参少于形参。形参会被赋值为`undefined`
* 实参多余形参。多传入的参数会被忽略，但是通过`arguments`对象可以获得。

## 强制参数
那么如果缺少参数的时候，我们可以进行抛出。
```
function foo(mandatory, optional) {
    if (mandatory === undefined) {
        throw new Error('Missing parameter: mandatory');
    }   
}
```

在ES6里，我们可以使用默认值来进一步优化这个场景。
```
function throwError() {
    throw new Error('Missing parameter');
}
function foo(param1 = throwError(), param2 = throwError()) {
    // do something
}
foo(10, 20);    // ok
foo(10);   // Error: missing parameter
```
也就是我们之前提到了，默认值可以设置函数。

## 总结
ES6带来的参数改进让我们几乎可以抛弃arguments了，更是由于严格模式下不支持。所以还是不要对arguments进行过多了使用了。

参考文章：
http://www.cnblogs.com/xiaoniuzai/p/6534360.html
http://www.cnblogs.com/moveofgod/archive/2012/11/03/2751745.html
http://www.w3school.com.cn/js/pro_js_functions_arguments_object.asp
http://www.cnblogs.com/Fskjb/archive/2011/10/27/2227111.html
