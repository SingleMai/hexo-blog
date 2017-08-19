---
title: ES6的新玩法
date: 2017-07-19 22:08:06
categories: 'js'
tags:
---

嘻嘻，就记录一些自己常用的ES6语法。毕竟更新地频繁，而且有些又不常用。所以就当做有条件地学习吧~~
<!--more-->
## 变量声明const和let
在ES5，我们不管声明任何变量，用的都是`var`关键字，但是导致了一个问题，就是无论声明在何处，都会被视为声明在函数的最顶部，也就是常说的**变量提升**。看两个例子感受一下ES6新关键字`let`带来的好处。

```
// ES5
function aa (){
    if(bool) {
        var test = 'hello'
    } else {
        console.log(test);
    }
}
//=> 编译后
function aa(){
    var test; // 变量提升
    if(bool) {
        test = 'hello'
    } else {
        console.log(test);
    }
}
```
很直观你就能感受到，其实变量提升的设计是为了简化一些工作。然而却带来了一系列不是我们预想的编译结果。例如，当条件满足，我才声明该变量的这种需求就变得难以满足。常常需要借助闭包来实现。但是`let`关键词出现之后，代码就要求更加规范了。因为

`let`**变量**关键字和`const`**常量**关键字都是块级作用域：
  - 在一个函数内部
  - 在一个代码块内部  

说的明白点，每个`{}`大括号内的代码块就是`let`和`const`的作用域。也就是有些操作，终于不需要闭包操作啦！！栗子：
```
//ES5
function aa(){
    var arry = [];
    for(var i = 0; i > 10; i++){
        arry.push((function (value){
            return function (){
                console.log(value)
            }(i))
        })
    }
}

//ES6
function aa(){
    let arry = [];
    for(let i = 0; i < 10; i++){
        arry.push(function(i){console.log(i)})
    }
}
```
很明显，闭包虽然是js的一大特性，但是写起来是真的麻烦。所以能简便这种不明确需要闭包技术的操作，何乐不为。

至于`const`关键字，就是常量，一经定义绝对不允许改变。所以当你有一个不想被改变的值的时候，就可以设置为const，这样可以避免重复时候发生的覆盖现象。

另外，在我遇到的情况下，用`var`的地方都可以完美地利用`const` 和 `let` 来代替。而且，`var`的提升容易造成问题，所以尽量使用新提供的关键字哦！

## 模板字符串
这这这，绝对是最最最最最实用的！！直接贴，这个功能绝对是比农奴解放还要伟大啊喂！

直接贴出三个用途吧：
1. 基本的字符串格式化。将表达式嵌入字符串中进行拼接。用${}来界定：最常用了啊
2. 在ES5时我们通过反斜杠(\)来做多行字符串或者字符串一行行拼接。ES6反引号(``)直接搞定。页面的渲染，再也不会乱糟糟一大坨了，耶~
```
// ES5
var msg = "Hi \
man!
"

// ES6
const template = `<div>
    <span>hello world</span>
</div>`
```

## 字符串方法
```
// 1.includes: 判断是否包含然后直接返回布尔值
let str = 'hahay'
console.log(str.includes('y')) // true
// 2.repeat: 获取字符串重复n次
let s = 'he'
console.log(s.repeat(3)) // 'hehehe'
//想起自己最近做的一个需求：a = a + a + a 瞬间爱上这个方法
// 如果你带入小数，调用Math.floor(num)来处理
```

## 函数
### 函数默认参数
以前，我们设置参数默认值是这样设置的
```
function action(num) {
    num = num || 200
    // 当传入num时，num为传入的值，
    // 当没有传入参数时，num既有了默认值200
    return num
}
```
但是这种方法是有bug的，也就是说当用户传入0的时候，num = 200;因为0的布尔值就是false。所以嘛，有问题哦！

ES6为参数提供了默认值，更加语义化，更加容易看到，而且也不需要查看源码就能读到这个值所支持的默认属性。除了丑了点，一开始不习惯，就没啥不好的啦~直接贴语法吧。
```
function action(num = 200) {

}
```
### 箭头函数
几乎是一提ES6就能想到箭头函数。箭头函数最直观的三个特点——
1. 不需要function关键字来创建函数。
2. 省略return关键字
3. 继承当前上下文的this关键字，这里是一个容易出bug的点。利用箭头函数，不会改变当前的this，不会改变当前的this，不会改变当前的this。

```
// 只有一个参数时，可以省略括号；函数是直接返回时，可以省略return 以及 {}
// 如此简便，你简直都不敢相信他是一个函数有木有
let people = name => 'hello' + name

// 一般的写法
let people = (name, age) => {
    const fullName = 'h' + name
    return fullName
}

```

### 扩展的对象功能
构建对象时候的各种简写, 虽然一开始写起来不方便而且还不爽。但是写久了，特别是数量特别多的时候，你就会觉得这个东西越看越可爱啊喂
```
let obj = {
    name, // 等同于name: name
    getName() { // 定义函数的简写方法
        console.log('名字')
    }
}
```
ES6对象提供了Object.assign({})这个方法来实现浅复制，就是JQ的那个`extend({})`啦。

### 结构以及展开运算符...
这个我已经写过啦。跑去看这篇文章吧。[ES6中的arguments对象和parameters对象](https://singlemai.github.io/2017/06/29/%E6%8A%80%E6%9C%AF/js/ES6%E4%B8%AD%E7%9A%84arguments%E5%AF%B9%E8%B1%A1%E5%92%8Cparamers%E5%AF%B9%E8%B1%A1/)

勉勉强强补写个语法给你看吧
```
const people = {
    name: 'xixia',
    age: 23
}
const {name, age} = people
console.log(name, age); // xixia 23
//数组
const color = ['red', 'blue']
const [first, second] = color
console.log(first) //'red'
console.log(second) //'blue'
```

这里看到一个对于Object而言，还可以用于组合新的对象。如果有重复的属性名，右边覆盖左边。其实就是`Object.assign({}, first, second)`。
```
const first = {
        a: 1,
        b: 2,
        c: 6,
    }
    const second = {
        c: 3,
        d: 4
    }
    const total = { ...first, ...second }
    console.log(total) // { a: 1, b: 2, c: 3, d: 4 }
```

## import 和 export
这个概念就是模块化了啦~~直接贴栗子，其实模块化知识没那么简单。
```
//全部导入
import people from './example'

//有一种特殊情况，即允许你将整个模块当作单一对象进行导入
//该模块的所有导出都会作为对象的属性存在
import * as example from "./example.js"

//导入部分
import {name, age} from './example'

// 导出默认, 有且只有一个默认
export default App

// 部分导出
export class App extend Component {};

```
导入的时候有无大括号的区别，找别人的总结啦：
```
1. 当用export default people导出时，就用 import people 导入（不带大括号）

2. 一个文件里，有且只能有一个export default。但可以有多个export。

3. 当用export name 时，就用import { name }导入（记得带上大括号）

4. 当一个文件里，既有一个export default people, 又有多个export name 或者 export age时，导入就用 import people, { name, age }

5. 当一个文件里出现n多个 export 导出很多模块，导入时除了一个一个导入，也可以用import * as example
```

## Promise
说白了就是用同步的方式去写异步代码。避免代码横向发展，进入回调地域。

咦，这个话题我好像也讨论过了？[解决回调地狱](https://singlemai.github.io/2017/05/20/%E6%8A%80%E6%9C%AF/node.js/%E8%A7%A3%E5%86%B3%E5%9B%9E%E8%B0%83%E5%9C%B0%E7%8B%B1/)

## Generators 生成器
嘻嘻 其实也写过。hmm 还是看上面的文章吧


http://www.jianshu.com/p/287e0bb867ae
