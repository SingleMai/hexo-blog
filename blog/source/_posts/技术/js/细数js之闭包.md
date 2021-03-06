---
title: 细数JS之闭包
date: 2017-09-08 09:55:43
categories: 'js'
tags:
  - '细数js'
---
闭包是一个经常会在各种设计模式里面和高阶教程出现的技术，学习以来总是不断回头重学重理解，好像是理解了？然而别人让我讲述的时候却不太懂如何表达？应该是理解不深刻吧，所以特意搜了好多几篇文章再加上红宝书犀牛书，结合着认真深刻学习一番~~
<!-- more -->
最近，突然发现如果用高中政治学到的3W法则来学习一个知识，往往能够比较全面（比较而已）。所以以后写作风格，稍微转换成这种写法，尝试一下
## 闭包是什么(What)
**闭包是指有权访问另一个函数作用域中的变量的函数。**（红宝书）
**函数对象可以通过作用域链相互关联起来，函数体内部的变量都可以保存在函数作用域内。**（犀牛书）
**当一个内部函数被其外部函数之外的变量引用时，就形成了闭包。**
一般说来，定义这玩意都是专业的说法，然而却不利于理解。因为严谨性，它需要很多名词定语搞在一起。最简单，先看一个简单的闭包例子。
```
function parent() { // 这是内部函数的外部函数
  function children() { // 这是内部函数
    console.log('hello world!');
  }
  return children; // 返回内部函数
}
let clo = parent(); // 外部函数之外的变量引用内部函数
```
通过这个小demo，我们可以稍微抛开严谨性来理解一下闭包。闭包，就是一个函数返回了一个它的内部函数。当这个函数被引用时，闭包就形成了。再具现成步骤：
1. 定义普通函数`parent`
2. 在`parent`函数里面定义一个普通函数`children`
3. 在`parent`中返回`children`函数
4. 执行`parent`并用将返回值结果赋值给外部变量
当然，实际中闭包的实现在第三第四步可能会有所不同。（因为取得引用的方式不单止返回值，也可以利用对象之类的操作）

## 为什么？(WHY)
理解闭包的形成，你需要掌握以下知识：执行环境、活动对象、作用域以及作用域链。

### 执行环境及作用域
执行环境定义了变量或函数有权访问的其他数据，决定了它们各自的行为。也许说执行环境，可能一会一时懵了，但是和你说执行环境就是常说的全局环境和局部环境这样可能就会好理解很多。我一直是认为执行环境和作用域是同一个东西。可是翻查了书本和各种文章都没有明确这样说明。**所以一下的解释我只能结合自己的知识来理解，执行环境是抽象的作用域，作用域是执行环境的具体实现（没有太多区别）**——也就是下面开始讨论的执行环境都是我们经常所指的函数作用域

### 活动对象
活动对象是指在函数被调用时，使用`arguments`和其他命名的参数的值来初始化函数的活动对象，也就是**变量对象**。如果这个环境是函数，则将其活动对象作为变量对象。稍微有点绕，大致用demo理解一下。
```
function parent() { // 这是一个函数作用域
  let par // 当parent被调用时，创建一个属于parent的变量对象（arguments、children、par)
  function children() { // 这是函数作用域的子作用域
    let chil; // 当children被调用时，创建一个属于children的变量对象（arguments、chil)
  }
  return children;
}
// 全局环境的变量对象（arguments、parents、clo）
let clo = parent(); // 执行父函数
clo(); //调用父函数的子函数
```
在这个demo中，就有三个执行环境。分别是`全局环境`、`parent()环境`、`children()环境`，每个环境都有各自对应的变量对象。

### 作用域链
代码的解析过程总是从下往下解析，但是遇到函数时，它需要进入到函数的环境中执行，执行完毕后会返回上一层继续执行。那这个过程是依赖什么进行的呢。这时候就是作用域链的出场啦。

**作用域链的用途，是保证对执行环境有权访问的所有变量和函数进行的有序访问。本质上是一个指向变量对象的指针列表，它只一弄但不实际包含变量对象。** 可以把作用域链看成一个栈的数据结构，当遇到一个新的执行环境被创建时（函数调用），它就会将新生成的活动对象的引用地址压入栈中，也就是`Array.unshift()`。当这个执行环境跑完了，并且没有被外部引用时，那么作用域链就会将其推出栈`Array.shift()`,也就是销毁作用域链。

而作用域链能干什么呢？它里面存放的都是各个变量名的东东，很明显。就是用于实现我们获取父级的变量~所以很容易能想到作用域是应该针对每个活动对象所独有的。所说压入推出的应该是当前执行环境的作用域。也就是代码刚生成时，每块执行环境都已经有了自己的不变作用域链，而我们讨论的变化的作用域链是随着代码的执行不断更改着我们当前环境的一种状态变化。这块同样是我个人的理解。不变是总体上来看的不变，变化是代码的执行过程

## GC回收机制
这里只是稍微提及，因为更多内容不会在这里多说。在 Javascript 中，如果一个对象不再被引用，那么这个对象就会被 GC 回收，否则这个对象一直会保存在内存中。

### 把概念综合起来再看
还是上面的例子，我们模拟着解析器的思路来执行上面的代码。
1. 首先，在全局环境中，先创建一个执行环境和它的变量对象(arguments、parent、 clo)引用推入作用域链中。（此时执行环境作用域链有1个）
2. 遇到执行`parent()`函数，再创建一个执行环境和它的变量对象(arguments、children、par)引用压入作用域中。（此时执行环境作用域链有2个）
3. 函数执行完毕返回一个`children`函数，并被赋值给了clo变量（clo获得了children函数的引用）。之后将执行环境的作用域链推出。（此时执行环境作用域链有1个）
4. 作用域链被退出，开始审查这块作用域链的变量对象是否有被外部作用域引用（也就是是否还在被使用着）。如果没有，则销毁（普通执行函数）；如果有，则它的活动对象仍然会留在内存中，直到引用取消。（闭包）（GC不回收）
5. 继续执行代码，`clo()`，调用`children()`函数。同样重复2的步骤，由于parent变量对象内存还存在，所以在children的函数内部依旧可以调用`par`这个变量。
6. 假设现在多在全局外执行一句`clo = null;` 那么这是引用取消了，闭包的内存也就释放了（GC回收）。

原来到现在为止，闭包的概念几乎是理完了。但是为了能够更好的理解闭包的应用。我们再讨论两个问题
### 再说说，闭包与变量（红宝书）
**闭包只能取得包含函数中任何变量的最后一个值。** 这句话如果有没细心观察demo还真的理解不了它的意思。直接看两个demo
```
function parents() {
  var result = new Array();
  for (var i = 0; i < 10; i++) {
    result[i] = function() {
      return i;
    }
  }
  return result;
}
parents();
```
这样的一个闭包，只能返回一个函数数组，表明上看，它返回的每个函数都有自己的索引。但是由于他们共同引用了i，而在最后才使用i。结果可想而知，当它使用i的时候，i已经变成了10.为了满足两个条件(返回是函数数组、并有各自索引)，我们需要创建另一个匿名函数来形成闭包。
```
function parents() {
  var result = new Array();
  for(var i = 0; i < 10; i++) {
    result[i] = function(i) {
      return function() {
        return i;
      }
    }(i)
  }
  return result
}
```
上面这方法虽然实现了需求，然而却累赘，繁琐，十分不好看，不优雅。有幸，我们迎来了ES6的`let`关键字。`let`关键字支持的块级作用域让我们终于可以不必为了模拟块级作用域而去编写闭包了。用demo1的代码就能轻松实现返回一个有各自索引的函数数组。其实就是把`var i = 0;`改成了`let i = 0;`
```
function parents() {
  var result = new Array();
  for (let i = 0; i < 10; i++) {
    result[i] = function() {
      return i;
    }
  }
  return result;
}
parents();
```

### 闭包与匿名函数与this
由于在实际应用中，我们往往会将闭包和匿名函数联系在一起使用。所以就会引发一个问题：**匿名函数的执行环境具有全局性，因此其this对象通常指向window**。栗子：
```
var name = "The Window";
var obj = {
  name: 'The Obj',
  getName: function() {
    return function() { // 匿名函数1
      return this.name
    }
  }
};
console.log(obj.getName()); // 返回匿名函数1
console.log(obj.getName()()); // The Window
```
为了避免这种`this`指向的该表，我们可以这样做
```
var name = "The Window";
var obj = {
  name: 'The Obj',
  getName: function() {
    var _this = this;
    return function() { // 匿名函数1
      return _this.name
    }
  }
};
```
或者这样
```
var name = "The Window";
var obj = {
  name: 'The Obj',
  getName: function() {
    return this.name
  }
};
obj.getName();
```

## 用途（WAY)
捣鼓了这么久闭包，总算达到应用层面的东西了。在解释why的时候，我们提到了一个闭包的作用——构建块级作用域，而且也知道了我们再也不用那样干了（除非为了兼容性）。那，闭包还有什么作用呢？

其实让我这么个新菜来分析闭包的各种用途，我可能说得并不够好，这篇文章的更多意义在于理解闭包，并在以后工作中遇到闭包能够快速理解并使用。稍微贴一些使用闭包的例子吧。

### 保存不污染全局的变量
当我们需要在模块中定义一些变量，并希望这些变量一直保存在内存中但又不会“污染”全局的变量时，就可以用闭包来定义这个模块。
```
function parent(){   
    var count = 0;   
    function children(){   
       count ++;   
       console.log(count);   
    }   
    return children;   
}
```
这样一来，可以将count给保护起来，不被外包函数所影响控制。操作count只能通过`parent()`进行。

### 编写独立组件
```
(function(document){   
    var viewport;   
    var obj = {   
        init:function(id){   
           viewport = document.querySelector("#"+id);   
        },   
        addChild:function(child){   
            viewport.appendChild(child);   
        },   
        removeChild:function(child){   
            viewport.removeChild(child);   
        }   
    }   
    window.jView = obj;   
})(document);
```

## 内存泄露
没错，闭包会造成内存泄露，这里也提到了手动释放内存的方法。更多篇幅到有关内存泄露的文章看吧。暂时没写，所以没办法提供链接跳转咯。
