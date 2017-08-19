---
title: JavaScript入门
date: 2017-2-20 16:32:44
categories: "js"
tags:
     - js
---

# JavaScript

js入门知识汇总
<!-- more -->
<!-- toc orderedList:0 depthFrom:1 depthTo:6 -->

* [JavaScript](#javascript)
  * [入门知识](#入门知识)
  * [面向对象的JavaScript](#面向对象的javascript)
    * [作用域](#作用域)
      * [词法作用域](#词法作用域)
      * [执行环境（内存中的作用域结构）](#执行环境内存中的作用域结构)
    * [闭包](#闭包)
    * [`this`关键字](#this关键字)
    * [原型链](#原型链)
    * [对象修饰模式](#对象修饰模式)
    * [函数类](#函数类)
    * [原型类](#原型类)
    * [伪类](#伪类)
    * [超类和子类](#超类和子类)
    * [伪类子类](#伪类子类)
  * [JQuery](#jquery)
  * [CSS canvas](#css-canvas)
  * [用CSS canvas 实现无声电影晕影](#用css-canvas-实现无声电影晕影)

<!-- tocstop -->

## 入门知识
  - 值类型：数值、布尔值、null、undefined  引用类型：对象、数组、函数。
  - 数据类型及定义：`var demo = num/string/boolean/[arry]`
  - 另外两种数据类型：`null` && `underfined`
    - `null`是没有值的数据类型——没有意义或者完全为空的值；`var x = null`
    - `underfined`是没有赋值的数据类型——没有值，连表示空的值也没有；`var x;`
  - 表示`false`的值:
    - `""`
    - `false`
    - `underfined`
    - `null`
    - `NAN`——not a number,即数字运算时出现错误，没有产生有效的数字。
  - 两种数据比较方式：
    - 隐式类型转换：== 和 != （自动转换成相应的数据类型进行比较）
    - 绝对相等：=== 和 !== （不进行数据类型转换）
  - 条件语句
    -`if (/* this expression is true */) {// run this code} else {// run this code}`
  - 逻辑运算符：与`&&` 或`||` 非 `!`
  - 三元运算符： `conditional ? (if condition is true) : (if condition is false)`
  - Switch语句：
  ```
      var tier = "nsfw deck";
      var output = "You’ll receive "

      switch (tier) {
        case "deck of legends":
          output += "a custom card, ";
        case "collector's deck":
          output += "a signed version of the Exploding Kittens deck, ";
        //default：表示的是如果都不符合则运行。（如果前者符合项没有break;语句那么也会传递执行）
        default:
          output += "one copy of the Exploding Kittens card game.";
      }
      console.log(output);
  ```
  - 循环——`While`与`For`
  - 函数——`function reheatPizza(numSlices) {// code that figures out reheat settings!}`
    - 函数的内嵌
  ```
    // function declaration that takes in two arguments: a function for displaying
    // a message, along with a name of a movie
    function movies(messageFunction, name) {
      messageFunction(name);
    }

    // call the movies function, pass in the function and name of movie
    movies(function displayFavorite(movieName) {
      console.log("My favorite movie is " + movieName);
    }, "Finding Nemo");
```
  - 提升
    - JavaScript 会将函数声明和变量声明提升到当前作用域的顶部。
    - 变量赋值不会提升。
    - 在脚本的顶部声明函数和变量，这样语法和行为就会相互保持一致。
  - 作用域
    - 如果标识符在全局作用域内声明，则可以到处访问。
    - 如果标识符在函数作用域内声明，则可以在所声明的函数内访问（甚至可以在函数中声明的函数内访问）。
    - 尝试访问标识符时，JavaScript 引擎将首先查看当前函数。如果没找到任何内容，则继续查看上一级外部函数，看看能否找到该标识符。将继续这么寻找，直到到达全局作用域。
    - 全局作用域不是很好的做法，可能会导致糟糕的变量名称，产生冲突的变量名称和很乱的代码。
    - 对象
      - 对于对象中的数据定义是JSON，可以运用[JSON检验](http://jsonlint.com/)来检验格式的正确。
  ```
        var myObj = {
          color: "orange",
          //数组
          shape: ["sphere"],
          //内嵌对象
          type: {
            type : "sphere"
            },
          eat: function() { return "yummy" }
        };
        myObj.eat(); // method
        myObj.color; // property
  ```
## 面向对象的JavaScript
### 作用域
  - 在某些环境中，全局作用域可以被多个不同文件共享；
#### 词法作用域
  - 每一个`function`都会创建一个词法作用域。（循环控制语句并不会创建作用域）
  - 如果使用了未经定义的参数，该属性将自动生成在全局作用域当中。
#### 执行环境（内存中的作用域结构）
  - 在代码运行时才被创建，而不是在代码键入时（词法作用域）。——每一次运行，都会创建一个作用域；
  - （如果没有保存函数使用权限）每一次运行函数，都会创建一个新的作用域；（当函数被使用时才创建）
### 闭包
  - 每个函数可以访问包含它的作用域中的所有变量。闭包就是在外部作用域都返回之后，仍可以访问任意函数。
  - 三种方法：
    - 将函数传入setTimeout
    - 通过外部函数来返回内部函数
    - 将内部函数保存为某个全局变量；一般都是用对象；如果是数组，然后在内部使用`.push`存入函数；
    - 使用闭包可以重复使用已经生成过的内存作用域。内存作用域会重新指向闭包所指向的作用域。
    - 重构代码的机会：每当一个函数的输入参数为静态时，也就是每次调用函数，该函数没有使用新值。
### `this`关键字
  - `this`参数和普通参数的区别
    - `this`参数没办法命名
    - 绑定参数的方式和绑定普通参数的方式不同
      - `obj.fn(3,4)`在这个例子中`this`绑定的就是obj
          - `fn(3,4)`当使用函数时，前面没有`.`，`this`自动指向全局作用域。从而有可能使某些参数出现`underfined`
          - `fn.call(r,a,b)`使用`.call`我们可以重写默认的全局对象绑定.`r`就是`this`
          - `r.method.call(y,a,b)`.这种情况`y`就是`this`
          - `setTimeout(r.method,1000)`,`this`仍是指向global。因为`method`函数调用在`setTimeout`内部并没有`.method`的标识
          - `setTimeout(function(){r.method(g,b)},1000)`这个调用方式，可以使`this`绑定`r`
          - `new r.method(g,b)`中`this`的指向不再绑定`r`,而是绑定`new`产生的对象
      - `this`不等于函数对象
      - 不等于函数实例
      - 不等于对象中的属性
      - 不等于对象中产生的内存作用域；
      - 不等于函数参数组成的产生内存作用域的对象；
### 原型链
  - 构造原型链：`var rose = Object.create(gold);//gold是原型链的上游`
  - 当在`rose`中查找不到想要的参数时，会到`gold`处查找。如果在当前对象找到了，则原型链不产生作用。
  - 原型链和一次性复制的区别：查找时，原型链能够保证时刻更新数据；而一次性复制则后续操作，两对象完全独立。
  - 对象原型是所有类型的顶端原型链。在对象原型中的大量方法例如`.toString()`与`this`结合，就实现了对`toString()`的调用。
  - `.constructor（）`是调用了对象原型中的方法--可以返回传入参数的类型。

### 对象修饰模式
  - 将大量的重复代码放入到一个构造函数中。（修饰器中）
  - 装饰器有点类似类中的方法。将一个对象传入后，进行属性的修改。

### 函数类
  - 函数类也就是一个构造器。其中函数代表的是构造函数，利用某类的构造函数得到的对象就是实例。将实例赋值则称为实例化。
  - 使用`car.method = function(){}`甚至可以为函数添加一个属性。本质上函数和对象几乎一致。在函数上添加属性（另一个函数）可以将函数封装在某个函数当中隐蔽起来。避免全局作用域有过多的对象属性；使用的是`extend`方法（自己实现），将一个函数中的属性全部复制到另一个函数中。

### 原型类
  - 是类的另一种实现方式。与函数类不同，原型类不需要将类的所有方法都复制到一个实例当中，而是采用原型链的方法对类方法进行移植。
  - `var obj = Object.create(Car.methods);`
  - 在函数产生的时候，会自动产生一个方法容器对象`key.prototype`.我们把我们的函数方法链接到当中。`.prototype.（method）`值得一提的是prototype这个属性是语言本身提供用来装载方法的。实际对内存没多大区别。是一个免费提供存储的容器，并不包含任何附加的特殊特性。
  - 每个`.prototype`拥有一个`.constructor`属性。这可以让我们判断哪个构造函数创建了哪个对象。也就是`Car.prototype.constructor`会找到`Car本身`
  - 运算符`instanceof`的使用：`amy instanceof Car`可以用来判断右侧运算对象的`.prototype`是否存在在左侧运算对象的原型链中。此运算符只对类适用，所有非类，也就是不是通过构造方法得到的对象都会委托查找到`Object.prototype`.
  - 一个实例化的对象委托查找会绑定到`key.prototype`上；但是`Car`类并不会委托查找到`Car.prototype`.在这里`Car`是一个函数对象，当它运行时，它会将由它产生的实例委托查找到`Car.prototype`
### 伪类
  ```
  var Car = function(loc){
    var obj = Object.create.(Car.prototype);
    ...
    return obj;
  }
  ```
  1. 观察以上构造类的代码。可以看出每构造一个类，第一行以及最后一行代码都是重复的。为了减少这些输入，JavaScript提供了关键字`new`来自动生成这两行代码。也就是在实例化对象的时候输入`var ben = new Car(1)`那么`Car`不需要输入这两行代码由语言自动插入生成。这样的类称为伪类。
  1. 伪类版本只是在原型模式上为了语法方便所加的薄薄一层。事实上，这两种模式的主要区别是JavaScript引擎已经实现性能优化的数目，并且这些优化是针对伪类模式的。

### 超类和子类
  1. 超类和子类，也就是父类和子类。类似于Java中子类继承父类属性方法。不同的是没有`extend`关键字，且父类本身是一个完整的类。子类继承父类的时候在构造方法中定义`var obj = Car(obj)`

### 伪类子类
  1. 构建伪子类
      1. 伪子类的定义`var Van = function(loc){Car.call(this,loc)}`目的是将`Van`的实例化对象绑定到`Car`对象上
      1. 子类原型委托（使得能调用父类方法）:`Van.prototype = Object.create(Car.prototype)`;使用`Van.prototype = new Car();`能够取得和上述语句的同样效果。不同的是这条语句会将`Car()`运行一遍，造成不必要的资源浪费。
      1. 添加`.constructor`属性：`Van.prototype.constructor = Van ;`
## JQuery
  - 引用`JQuery`到页面中:`<script src = '//code.jquery.com/jquery-1.11.1.min.js'></script>`
  - `$`等于`jQuery`，是调用`jQuery`的简写符号
  - `$("#main/.main/h1")`选择器
  - DOM选择的方法：
    - `.parent()` 向上搜索1层的父类元素
    - `.parents()` 向上搜索所有的父类
    - `.children()` 向下搜索1层的子类
    - `.find()` 向下搜索所有的子类
    - `.siblings('h1')` 搜索兄弟类--在括号内内嵌搜索器，会更精确找到想找的元素
    - 事件处理
    ```
    $( 'article' ).on( 'click', function( evt ) {
        console.log( evt );
    });
    ```
  - 更多方法文档应该查询技术文档。
## CSS canvas
  1. html:`<canvas id= "c" width="200" height="200"></canvas>`
  2. js:
  ```
    var c = docunment.querySelector("#c");
    var ctx = c.getContext("2d");
    var image = new Image();
    image.onload = function(){
      console.log("Loader image");
      //do something
      ctx.drawImage(image,0,0,c.width,c.height);
      //画的图片，图片开始的x、y坐标，图片宽高；
    }
    image.src = "#";
  ```


## 用CSS canvas 实现无声电影晕影
无声电影晕影
如果你目前无法跟上这些链接中的所有代码，别担心！试着从高层次看待它们，不要过多地担心语法。

Tanner Helland 在[伪代码和视觉基础知识](http://www.tannerhelland.com/3643/grayscale-image-algorithm-vb6/)中概括了大量用于计算灰度的不同算法。 Mozilla 具有关于如何执行[绿屏效果](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API/Manipulating_video_using_canvas)的出色教程。

下面列出了我们未在代码中创建的视觉效果和资源：

Attributions
City Background
Title: What Happened on Twenty-Third Street, New York City (1901)
Director: Edwin S. Porter
Production Company: Edwin S. Porter
Sponsor: Edison Mfg. Co.
https://archive.org/details/What_Happened_1901

Train Background
Title: Freight Train (1898)
Producer: The Edison Manufacturing Co. and Thomas A. Edison, Inc.
https://archive.org/details/EdisonMotionPicturesCollectionPartOne1891-1898
https://archive.org/download/EdisonMotionPicturesCollectionPartOne1891-1898/1898Freight_train.mpg

Music
Title: Old Fasioned Auto Piano.wav
Producer: Razzvio
http://www.freesound.org/people/Razzvio/sounds/79572/

Title Card Design
Name: Silent Movie The End Title Card HD
Creator: Farrin N. Abbott / CopyCatFilms

滤镜和效果
Night Vision Scope

Creator: Andrew Kramer / Video Copilot http://www.videocopilot.net/blog/2012/11/new-tutorial-simulated-scopes/
