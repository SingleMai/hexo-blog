---
title: javascript事件循环
date: 2017-10-08 23:06:43
categories: 'js'
---
这篇更多是科普文~摘录文，可以直接跳转到文末找原文链接。
<!-- more -->
前面的一些废话：其实对别人总结的知识原封不动的搬过来总是显得有那么些不好。但是转念一想，我们应该承认自己的弱小，虚心接受别人总结的内容，即使是按着原文敲一遍文字，在脑海里烙印下痕迹...这样积累下来，总有一天也能总结出自己的精华，反哺回来~~好了，废话不多说，开始学习吧~

## 1.事件循环机制详解与实践应用
JavaScript是典型的单线程单并发语言，即表示在同一时间片内其职能执行单个任务或者部分代码片段。换言之，我们可以认为某个同域浏览器上下文中JavaScript主线程拥有一个函数调用栈以及一个任务队列；主线程会依次执行代码，当遇到函数时，会先将函数入栈，函数运行完毕后再将该函数出栈，知道所有代码执行完毕。当函数调用栈为空时，运行时即会根据事件循环(event loop)机制来从任务队列中提取出待执行的回调并执行，执行的过程同样会进行函数帧的入栈出栈操作。每个线程有自己的事件循环，所以每个Web Worker有自己的，所以它才可以独立执行。然而，所有同属一个origin的窗体都共享一个事件循环，所以它们可以同步交流。

Event Loop（事件循环）并不是 JavaScript 中独有的，其广泛应用于各个领域的异步编程实现中；所谓的 Event Loop 即是一系列回调函数的集合，在执行某个异步函数时，会将其回调压入队列中，JavaScript 引擎会在异步代码执行完毕后开始处理其关联的回调。

在 Web 开发中，我们常常会需要处理网络请求等相对较慢的操作，如果将这些操作全部以同步阻塞方式运行无疑会大大降低用户界面的体验。另一方面，我们点击某些按钮之后的响应事件可能会导致界面重渲染，如果因为响应事件的执行而阻塞了界面的渲染，同样会影响整体性能。实际开发中我们会采用异步回调来处理这些操作，这种调用者与响应之间的解耦保证了 JavaScript 能够在等待异步操作完成之前仍然能够执行其他的代码。Event Loop 正是负责执行队列中的回调并且将其压入到函数调用栈中，其基本的代码逻辑如下所示：
```
while (queue.waitForMessage()) {
  queue.processNextMessage();
}
```

在 Web 浏览器中，任何时刻都有可能会有事件被触发，而仅有那些设置了回调的事件(即有监听回调函数的)会将其相关的任务压入到任务队列中。回调函数被调用时即会在函数调用栈中创建初始帧，而直到整个函数调用栈清空之前任何产生的任务**都会被压入到任务队列中延后执行**；顺序的同步函数调用则会创建新的栈帧。总结而言，浏览器中的事件循环机制阐述如下：
- 浏览器内核会在其它线程中执行异步操作，当操作完成后，将操作结果以及事先定义的回调函数放入javascript主线程的**任务队列中。**
- javascript主线程会在执行栈清空后，读取任务队列，读取到任务队列中的函数后，将该函数入栈，一直运行直到执行栈清空，再次去读取任务队列。不断循环。（也就是常说的读一条任务跑一条）
- 当主线程阻塞时，任务队列仍然是能够被推入任务的。这也就是为什么当页面JavaScript进程阻塞时，我们出发的点击等事件，会在进程恢复后依次执行。

## 2.函数调用栈与任务队列
 JavaScript 内存模型的角度，我们可以将内存划分为调用栈（Call Stack）、堆（Heap）以及队列（Queue）等几个部分：

 其中的调用栈会记录所有的函数调用信息，当我们调用某个函数时，会将其参数与局部变量等压入栈中；在执行完毕后，会弹出栈首的元素。而堆则存放了大量的非结构化数据，譬如程序分配的变量与对象。队列则包含了一系列待处理的信息与相关联的回调函数，每个 JavaScript 运行时都必须包含一个任务队列。当调用栈为空时，运行时会从队列中取出某个消息并且执行其关联的函数（也就是创建栈帧的过程）；运行时会递归调用函数并创建调用栈，直到函数调用栈全部清空再从任务队列中取出消息。换言之，譬如按钮点击或者 HTTP 请求响应都会作为消息存放在任务队列中；需要注意的是，仅当这些事件的回调函数存在时才会被放入任务队列，否则会被直接忽略。（这块知识抛却任务队列，在另一篇执行上下文的文章已经讲得很清楚啦）

 这里还值得一提的是，Promise.then 是异步执行的，而创建 Promise 实例 （executor） 是同步执行的，譬如下述代码：
 ```
 (function test() {
    setTimeout(function() {console.log(4)}, 0);
    new Promise(function executor(resolve) {
        console.log(1);
        for( var i=0 ; i<10000 ; i++ ) {
            i == 9999 && resolve();
        }
        console.log(2);
    }).then(function() {
        console.log(5);
    });
    console.log(3);
})()

// 输出结果为：
// 1
// 2
// 3
// 5
// 4
 ```

## 3.Macro Task(TASK) 与 Micro Task(Job)
一次事件循环：先运行macroTask队列中的一个，然后运行microTask队列中的所有任务。接着开始下一次循环（只是针对macroTask和microTask，一次完整的事件循环会比这个复杂的多）。

其中macroTask和microTask是两种任务队列，相比而言，大家更熟悉的一个词是任务队列（task queue,其实就是macroTask）,大家更熟悉的关于事件循环的机制说法大概是：主进程执行完了之后，每次从任务队列里取一个任务执行。但是promise出现之后，这个说法就不太准确了。

JavaScript引擎对这两种队列有不同的处理，简单的说就是引擎会把我们的所有任务分门别类，一部分归为macroTask，另外一部分归为microTack，下面是类别划分：
> macroTask: setTimeout, setInterval, setImmediate, requestAnimationFrame, I/O, UI rendering
> microTask: process.nextTick, Promise, Object.observe, MutationObserver

### 实践：小二，上代码
我们以setTimeout、process.nextTick、promise为例直观感受下两种任务队列的运行方式。
```
console.log('main1');

process.nextTick(function() {
    console.log('process.nextTick1');
});

setTimeout(function() {
    console.log('setTimeout');
    process.nextTick(function() {
        console.log('process.nextTick2');
    });
}, 0);

new Promise(function(resolve, reject) {
    console.log('promise');
    resolve();
}).then(function() {
    console.log('promise then');
});

console.log('main2');
```
别着急看答案，先以上面的理论自己想想，运行结果会是啥？

最终结果是这样的：
```
main1
promise
main2
process.nextTick1
promise then
setTimeout
process.nextTick2
```
process.nextTick 和 promise then在 setTimeout 前面输出，已经证明了macroTask和microTask的执行顺序。但是有一点必须要指出的是。上面的图容易给人一个错觉，就是主进程的代码执行之后，会先调用macroTask，再调用microTask，这样在第一个循环里一定是macroTask在前，microTask在后。

但是最终的实践证明：在第一个循环里，process.nextTick1和promise then这两个microTask是在setTimeout这个macroTask里之前输出的，这是为什么呢？

因为主进程的代码也属于macroTask（这一点我比较疑惑的是主进程都是一些同步代码，而macroTask和microTask包含的都是一些异步任务，为啥主进程的代码会被划分为macroTask，不过从实践来看确实是这样，而且也有理论支撑：【翻译】Promises/A+规范）。

主进程这个macroTask（也就是main1、promise和main2）执行完了，自然会去执行process.nextTick1和promise then这两个microTask。这是第一个循环。之后的setTimeout和process.nextTick2属于第二个循环

别看上面那段代码好像特别绕，把原理弄清楚了，都一样 ~

requestAnimationFrame、Object.observe(已废弃) 和 MutationObserver这三个任务的运行机制大家可以从上面看到，不同的只是具体用法不同。重点说下UI rendering。在HTML规范：event-loop-processing-model里叙述了一次事件循环的处理过程，在处理了macroTask和microTask之后，会进行一次Update the rendering，其中细节比较多，总的来说会进行一次UI的重新渲染。

## 4.浅析 Vue.js 中 nextTick 的实现
在 Vue.js 中，其会异步执行 DOM 更新；当观察到数据变化时，Vue 将开启一个队列，并缓冲在同一事件循环中发生的所有数据改变。如果同一个 watcher 被多次触发，只会一次推入到队列中。这种在缓冲时去除重复数据对于避免不必要的计算和 DOM 操作上非常重要。然后，在下一个的事件循环“tick”中，Vue 刷新队列并执行实际（已去重的）工作。Vue 在内部尝试对异步队列使用原生的 Promise.then 和 MutationObserver，如果执行环境不支持，会采用 setTimeout(fn, 0) 代替。

而当我们希望在数据更新之后执行某些 DOM 操作，就需要使用 nextTick 函数来添加回调：
```
// HTML
<div id="example">{{message}}</div>

// JS
var vm = new Vue({
  el: '#example',
  data: {
    message: '123'
  }
})
vm.message = 'new message' // 更改数据
vm.$el.textContent === 'new message' // false
Vue.nextTick(function () {
  vm.$el.textContent === 'new message' // true
})
```
在组件内使用 vm.$nextTick() 实例方法特别方便，因为它不需要全局 Vue ，并且回调函数中的 this 将自动绑定到当前的 Vue 实例上：
```
Vue.component('example', {
  template: '<span>{{ message }}</span>',
  data: function () {
    return {
      message: '没有更新'
    }
  },
  methods: {
    updateMessage: function () {
      this.message = '更新完成'
      console.log(this.$el.textContent) // => '没有更新'
      this.$nextTick(function () {
        console.log(this.$el.textContent) // => '更新完成'
      })
    }
  }
})
```
src/core/util/env
```
/**
 * 使用 MicroTask 来异步执行批次任务
 */
export const nextTick = (function() {
  // 需要执行的回调列表
  const callbacks = [];

  // 是否处于挂起状态
  let pending = false;

  // 时间函数句柄
  let timerFunc;

  // 执行并且清空所有的回调列表
  function nextTickHandler() {
    pending = false;
    const copies = callbacks.slice(0);
    callbacks.length = 0;
    for (let i = 0; i < copies.length; i++) {
      copies[i]();
    }
  }

  // nextTick 的回调会被加入到 MicroTask 队列中，这里我们主要通过原生的 Promise 与 MutationObserver 实现
  /* istanbul ignore if */
  if (typeof Promise !== 'undefined' && isNative(Promise)) {
    let p = Promise.resolve();
    let logError = err => {
      console.error(err);
    };
    timerFunc = () => {
      p.then(nextTickHandler).catch(logError);

      // 在部分 iOS 系统下的 UIWebViews 中，Promise.then 可能并不会被清空，因此我们需要添加额外操作以触发
      if (isIOS) setTimeout(noop);
    };
  } else if (
    typeof MutationObserver !== 'undefined' &&
    (isNative(MutationObserver) ||
      // PhantomJS and iOS 7.x
      MutationObserver.toString() === '[object MutationObserverConstructor]')
  ) {
    // 当 Promise 不可用时候使用 MutationObserver
    // e.g. PhantomJS IE11, iOS7, Android 4.4
    let counter = 1;
    let observer = new MutationObserver(nextTickHandler);
    let textNode = document.createTextNode(String(counter));
    observer.observe(textNode, {
      characterData: true
    });
    timerFunc = () => {
      counter = (counter + 1) % 2;
      textNode.data = String(counter);
    };
  } else {
    // 如果都不存在，则回退使用 setTimeout
    /* istanbul ignore next */
    timerFunc = () => {
      setTimeout(nextTickHandler, 0);
    };
  }

  return function queueNextTick(cb?: Function, ctx?: Object) {
    let _resolve;
    callbacks.push(() => {
      if (cb) {
        try {
          cb.call(ctx);
        } catch (e) {
          handleError(e, ctx, 'nextTick');
        }
      } else if (_resolve) {
        _resolve(ctx);
      }
    });
    if (!pending) {
      pending = true;
      timerFunc();
    }

    // 如果没有传入回调，则表示以异步方式调用
    if (!cb && typeof Promise !== 'undefined') {
      return new Promise((resolve, reject) => {
        _resolve = resolve;
      });
    }
  };
})();
```

----
[JavaScript Event Loop 机制详解与 Vue.js 中实践应用](https://zhuanlan.zhihu.com/p/29116364)
[【第993期】总是一知半解的Event Loop](https://mp.weixin.qq.com/s/3-8kH1L-FZqSgv8zocoY7g)
