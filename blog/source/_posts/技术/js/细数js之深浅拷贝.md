---
title: 细数js之深浅拷贝
date: 2017-12-28 14:43:03
categories: 'js'
tags:
  - '细数js'
---
今天在掘金社区看到了有关深浅拷贝的文章,发现和之前内心构想的代码并不一致,所以就开始认真地查看起来了=.=
<!-- more -->
### 什么是深拷贝/浅拷贝
一言蔽之,深拷贝就是复制的程度更深.浅拷贝就是复制的程度比较浅.
当然啦,这是对于已经熟悉这个概念的人来记忆的.不熟悉的话还是需要再深度剖析一下个中的.众所众知,js的对象是一个引用数据类型,也就是说在栈内存生成的是引用地址,当你进行取值的时候就会根据引用地址到堆内存取值.这有点像windows桌面的快捷方式.快捷方式是一个引用地址,当你双击它的时候,它就会跑到对应的文件目录执行那个文件.

所以深拷贝是只限于Object和Array这种引用数据类型的对象.如果熟悉VUE的话,大家应该会对这个深浅拷贝有足够多的理解.
- 浅拷贝,就是快捷方式,拷贝出来的对象(数组)最终会指向同一块引用地址.
- 深拷贝,重新建立了另一块堆内存,是另一个引用地址.两个对象之间没有任何联系.除了看起来一样.

### 怎么实现深拷贝
深拷贝的目的就是抛弃掉原本的引用地址,所以最根本的做法就是将原来的引用地址给切割断就能实现深拷贝.

#### 方式一
这是最简单的方式,也是Vue代码中能经常看到的一种拷贝方式.直接将原本的对象转换为字符串(取消原本的引用关系)后转换回对象新建新的引用地址.
```
var newObj = JSON.parse(JSON.stringify(obj);
```

#### 方式二
略微复杂,但是对于大数据量的数据,会有比较好的性能优化.这里就不解释了,自己看代码吧.
```
function deepClone2 (obj) {
  if (!obj && !(obj instanceof Object)) {
    return;
  }
  let newObj = null;
  if (obj instanceof Array) {
    // 数组
    newObj = [];
    for (let i = 0; i < obj.length; i++) {
      const item = obj[i];
      if (item instanceof Object) {
        newObj[i] = deepClone2(item);
      } else {
        newObj[i] = item;
      }
    }
    return newObj
  } else {
    // 对象
    newObj = {};
    for (const key in obj) {
      if (obj[key] instanceof Object) {
        newObj[key] = deepClone2(obj[key]);
      } else {
        newObj[key] = obj[key]
      }
    }
    return newObj;
  }
}
```

#### 方式三,在文章中被提到的方法
```
function deepClone(obj) {
  if (!obj && typeof obj !== 'object') {
    return;
  }
  var newObj = obj.constructor === Array ? [] : {};
  for (var key in obj) {
    if (obj[key]) {
      if (obj[key] && typeof obj[key] === 'object') {
        newObj[key] = obj[key].constructor === Array ? [] : {};
        //递归
        newObj[key] = deepClone(obj[key]);
      } else {
        newObj[key] = obj[key];
      }
    }
  }
  return newObj;
}
```

### 效率问题
由于不确认自己的想法是否正确,所以就想到自己做一下性能测试.这里深复制一个20000行左右的数据.由此可见,数据量不大的情况建议直接使用`JSON`转换,便捷又性能不错.如果数据量太大,则建议使用我自己写的那套.至于在文章中提到的,感觉性能过于差.因为使用`for in`是不得已才使用的循环方式,它对性能的支持有点差.
```
deep1: 1.793ms
deep2: 1.405ms
deep3: 3.144ms
```
### 补充一点`Obejct.assign()`
```
Object.assign(target, obj);
```
被书写规范列入了添加对象的键值推荐方式.但是值得注意的是,这个函数是一个浅拷贝.也就是
```
var a = { a: 1 };
var b = { b: 2 };
var c = Object.assign(a, b);
console.log(c);// {a:1, b:2}
a.a = 2; // { a: 2 }
console.log(c);// {a:2, b: 2}
```
这里触发了this的隐式调用,容易造成错误.所以我推测书写规范使用这个函数的前提是,这样书写
```
Object.assign(target, {}); // 通过匿名生成一个引用对象从而确保只有target能改变它的值
```

---

原文:https://juejin.im/post/5a0c199851882531926e4297
