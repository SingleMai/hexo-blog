---
title: 细数js之for循环
date: 2017-09-18 14:43:03
categories: 'js'
tags:
  - '细数js'
---

本来就我个人而言，我也没讲for循环想得那么仔细过。只是偶尔在实践中遇到过这些问题，既然有这种总结性文章，那必须转载起来~！~
<!-- more -->
在ES5中，有三种形式的for循环：
  1. 简单的for循环
  2. for-in循环
  3. forEach
在ES6中，新增加一种：
  4. for-of循环

## 简单for循环
这个是很常见的写法啦~有个优化的写法。如果数组长度在循环过程中不会改变时，我们应该讲数组长度用变量存储起来。
```
const arr = [1, 2, 3];
for (for i = 0, len = arr.length; i < len; i++) {
  console.log(arr[i]);
}
```

## for-in
虽然这个方法其实是用来遍历对象的，但是其实也可以用来遍历数组。只是有些特殊情况可以给讨论了~~
```
const arr = [1, 2, 3];
let index;
for(index in arr) {
  console.log('arr[' + index + '] = ' + arr[index]);
}
```
一般情况下，运行结果如下：
```
arr[0] = 1
arr[1] = 2
arr[2] = 3
```
但是由于`for-in`是用来遍历对象的，所以
```
const arr = [1, 2, 3];
arr.name = "hello"
let index;
for(index in arr) {
  console.log('arr[' + index + '] = ' + arr[index]);
}
输出结果变成这样
arr[0] = 1
arr[1] = 2
arr[2] = 3
arr[name] = 'hello'
```
于是，我们就会发现Array的一个真相
### Array的真相
Array 在 Javascript 中是一个对象， Array 的索引是属性名。事实上， Javascript 中的 “array” 有些误导性， Javascript 中的 Array 并不像大部分其他语言的数组。
首先， **Javascript 中的 Array 在内存上并不连续，其次， Array 的索引并不是指偏移量。**
实际上， **Array 的索引也不是 Number 类型，而是 String 类型的。** 我们可以正确使用如 arr[0] 的写法的原因是语言可以自动将 Number 类型的 0 转换成 String 类型的 "0" 。
**所以，在 Javascript 中从来就没有 Array 的索引，而只有类似 "0" 、 "1" 等等的属性。** 有趣的是，每个 Array 对象都有一个 length 的属性，导致其表现地更像其他语言的数组。但为什么在遍历 Array 对象的时候没有输出 length 这一条属性呢？那是因为 `for-in` 只能遍历“可枚举的属性”， length 属于不可枚举属性，实际上， Array 对象还有许多其他不可枚举的属性。**for-in 不仅仅遍历 array 自身的属性，其还遍历 array 原型链上的所有可枚举的属性。**

这里虽然说了forin的那么多缺点，但是它面对稀疏数组的时候还是很有用的。例如，for-in遍历一个稀疏数组，只会遍历对应的属性。然而用普通for循环实现，则会有很多无必要的浪费。
```
var arr = [];
arr[100] = 1;
arr[200] = 3;
// 这里for-in只会遍历2次。而普通for循环则会遍历200次
```

### for-in的性能
正如上面所说，每次迭代操作会同时搜索实例或者原型属性， for-in 循环的每次迭代都会产生更多开销，因此要比其他循环类型慢，一般速度为其他类型循环的 1/7。因此，除非明确需要迭代一个属性数量未知的对象，否则应避免使用 for-in 循环。如果需要遍历一个数量有限的已知属性列表，使用其他循环会更快，比如下面的例子：
```
const obj = {
  'prop1': "value1",
  'prop2': 'value2'
}
const props = ['prop1', 'prop2'];
for (let i = 0; i < props.length; i++) {
  console.log(obj[props[i]]);
}
```
上面代码中，将对象的属性都存入一个数组中，相对于 for-in 查找每一个属性，该代码只关注给定的属性，节省了循环的开销和时间。

## forEach
```
const arr = [1,2,3];
arr.forEach((data) => {
    console.log(data);
})
```
需要注意的是，forEach 遍历的范围在第一次调用 callback 前就会确定。调用forEach 后添加到数组中的项不会被 callback 访问到。如果已经存在的值被改变，则传递给 callback 的值是 forEach 遍历到他们那一刻的值。已删除的项不会被遍历到。

这里的 index 是 Number 类型，并且也不会像 for-in 一样遍历原型链上的属性。

所以，使用 forEach 时，我们不需要专门地声明 index 和遍历的元素，因为这些都作为回调函数的参数。

其他有关forEach以及ES5定义更多有关数组的遍历方法在细数js数组中有详细记叙。

## for-of
直接看用法
```
const arr = [1,2,3];
for(let data of arr) {
  console.log(data); // 1 ,2 ,3
}
```
## 为什么要引进for-of
要回答这个问题，我们先来看看ES6之前的 3 种 for 循环有什么缺陷：
- forEach 不能 break 和 return；
- for-in 缺点更加明显，它不仅遍历数组中的元素，还会遍历自定义的属性，甚至原型链上的属性都被访问到。而且，遍历数组元素的顺序可能是随机的。

因此为了改进，for-of能干什么呢
- 跟 forEach 相比，可以正确响应 break, continue, return。
- for-of 循环不仅支持数组，还支持大多数类数组对象，例如 DOM nodelist 对象。
- for-of 循环也支持字符串遍历，它将字符串视为一系列 Unicode 字符来进行遍历。
- for-of 也支持 Map 和 Set （两者均为 ES6 中新增的类型）对象遍历。

总结一下，for-of 循环有以下几个特征：
- 这是最简洁、最直接的遍历数组元素的语法。
- 这个方法避开了 for-in 循环的所有缺陷。
- 与 forEach 不同的是，它可以正确响应 break、continue 和 return 语句。
- 其不仅可以遍历数组，还可以遍历类数组对象和其他可迭代对象。

但需要注意的是，for-of循环不支持普通对象，但如果你想迭代一个对象的属性，你可以用for-in 循环（这也是它的本职工作）。

最后要说的是，ES6 引进的另一个方式也能实现遍历数组的值，那就是 Iterator。上个例子：
```
const arr = [1,2,3];
const iter = arr[Symbol.iterator]();

iter.next() //{value: '1', done:false}
iter.next() //{value: '2', done:false}
iter.next() //{value: '3', done:false}
iter.next() //{value: 'undefined', done:true}
```
----
转载至：
[【第786期】深入了解 JavaScript 中的 for 循环](https://mp.weixin.qq.com/s/BmSeCZFZrpkCGk_mmCYNAQ)
