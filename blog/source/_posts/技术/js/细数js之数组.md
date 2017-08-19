---
title: 细数JS之数组
date: 2017-08-04 23:06:43
categories: 'js'
tags:
  - '细数js'
---
细数JS之数组，嘻嘻是这系列的第一篇。我一直有的感悟，书可以读上好几遍，每遍都有更多的感悟。特别是编程类的书籍。So，给自己的暑假定了这个任务。废话不多说，开干吧！
<!--more-->
## `length`属性
`length`属性可以说是数组的标志性属性。当然也不是说有`length`属性就一定是数组（伪数组），但是是数组就一定会有`length`。这里把它特意拉出来，是想介绍它一个比较`hack`的操作。

在每一门编程语言中，都有定义不可操作属性，只读属性这些权限问题。JS当然也是如此，但是有一点不太让你适应的就是`length`属性居然是可操作的。也就是说它不是只读的。`length`属性和数组个数是具有双向绑定效果的。换句话说，当你调整`length`属性长度的时候，数组的长度是真的会被改变哦
```
let arr1 = [1, 2, 3];
console.log(arr1.length); // 3
arr1.length = 2;
console.log(arr1); // 1, 2
```
很神奇对吧！你肯定会想，那如果这时候我把数组长度设置回去呢？双向绑定嘛，那么那个数组就会变成这样的呀。
```
arr1.length = 3;
console.log(arr1); // 1, 2, undefined
```
也就是设置长度，相当于你声明了一个新的数组空位。只是还没定义值而已。

另外还有一点值得注意的就是，数组不允许出现中间断层。什么意思？也就是你的数组的下标必须是0,1,2,3一个一个排下去，决不允许0,1,2,4。看到这种说法，你肯定又想自己捣鼓点骚操作，欺负一下浏览器引擎对吧。
```
let arr2 = [1,2,3];
// 这时候数组长度为3，偏偏就是不设置4号位置的数值
arr2[10] = 1; //营造断层
console.log(arr2) // [1, 2, 3, undefined × 7, 1] length = 11
```

最后补充回一开始的那个hack。就是快速地删除清空数组呀。不过这种做法是否被推荐，我也不肯定啦。大家看情况呗？

## 检测数组
一提到检测类型，你首先会想到什么呢？一定是`typeof`操作符对吧？可是在这里行不通哦，因为`typeof`用来检测数值类型才是有效的。那么就要用`instanceof`了对吧！没错！这次你就答对啦~~~但是使用`instanceof`的问题是在Web浏览器中有可能存在多个窗体，每个窗口都有各自的全局对象，因此一个窗体的对象不可能是另一个窗体的构造函数实例，所以不是一个可靠的检测方法。
```
console.log(arr1 instanceof Array); // true
```
那有没有其他的方法呢？方法还是有的，嘻嘻嘻，不过只支持*ie9以上哦*，其实实现起来效果和`instanceof`一样，但是还多一个兼容性问题，所以才没人用吧~~也可能是我接触的比较少，这个方法的确没怎么看人用。嘻嘻。
```
Array.isArray(arr2); // true
```

## 数组操作
### `.push(val)`
在末尾插入新值并返回数组长度。相当于`arr1[arr1.length] = val`

### `.pop(val)`
删除数组末尾第一个值，并返回移除项。相当于`arr1.length = arr1.length - 1`少了获取值的操作。从这个方法看，`pop()`方法和直接操作数组长度还是有优势的

### `.unshift(val)`
和push相反，在数组头部插入新值并返回数组长度

### `.shift()`
不用说也能猜出来啦，在数组头部删除值并返回

### `.reverse()`
数组的反转，也是不用细说的东西吧？
```
let arr1 = [1, 2, 3];
arr1.reverse();
console.log(arr1); // 3, 2, 1
```

### `.sort()`
不传值的时候，排序的规则是字符串的比较大小。所以一般都需要你传入一个比较函数来决定你是打算升序还是降序。（大家一般能想到排序，一般都是数字吧，所以接下来介绍的也是数值的传函数方法）

先把我总结的口诀给大家。**降序，第一个值比第二个值小需要调换位置。升序，第一个值比第二个值大是不需要调换位置的**。好了，大家默念上面那一句三次，然后我们来看怎么理解它。

```
// 这是一个降序用的比较函数
function compare(value1, value2) {
  if (value1 < value2){ // 当第一个值比第二个值小
    return 1; // 需要调换位置，传1 => true
  } else if (value1 > value2) {
    return -1; // 否则不需要调换， 传-1 => false
  } else {
    return 0; // 虽然0也是false，但是其实是相等的情况啦
  }
}

// 然后来看这个简化版的栗子
// 同样按照口诀,可以先思考一下。我把思路写在下面
function compare(value1, value2) {
  return value1 - value2
}
// 假如，第一个值小于第二个值，那么返回小于0的数(false)，就是不需要调换位置。
// 那么很明显，这个就是升序的函数。其实有点绕，建议大家多练习一下。
```

### 合并数组`.concat(arr2)` 和拆分数组成字符串`.join()`
合并很简单，就是数组拼接。注意的是，它**不修改原数组**，返回的是一个新的数组。下面介绍ES6的数组新特性会提到更优雅的实现方式。

拆分其实也很简单，但是有一些小地方需要注意。将数组变为字符串有很多方法。`.toString()`、`.toLocalString()`、`.valueOf()`。不过这里推荐用`.join()`，原因就是可以传入分隔符定制化字符串，其他方法都是默认用`,`分割

### `.slice()`获取数组中的某一段数组
`.slice()`可以传入两个位置，取得两位置间的数组。**不修改原数组**。

### `.splice()`操作数组最强大方法。
为什么说他强大呢，因为他可以在指定位置插入指定值。同样可以在指定位置删除值。

`.splice(需要操作的数组下标，需要删除值的个数，需要插入的新值)`

直接贴几个demo，让你感受他的魅力
```
let arr1 = [1, 2, 3];
arr1.splice(0, 1); // 从0号下标开始，删除一个值。返回这个被删除的值
console.log(arr1); // 2, 3
arr1.splice(0, 0, 1); // 从0号下标开始，删除0个值，添加1这个值进去。
console.log(arr1) // 1,2,3
arr1.splice(0,2,[4,5,6]); // 从0号下标开始，删除2个值，并添加一个数组
console.log(arr1); // [4,5,6],3
```
值得注意的是，`.splice()`方法返回值永远是被删除的值。如果没有删除，那就返回空数组。

## 位置方法`indexOf()`、`lastIndexOf()`
`indexOf()`、`lastIndexOf()`这一对方法，都是查找数组是否存在指定值，如果有则返回下标，没有返回-1.挺好用的方法，只可惜是只支持**ie9以上**

## 迭代方法`.every()`、`.filter()`、`.forEach()`、`.map()`、`.some()`
### `.every()` 方法
检测数组所有元素是否都符合指定条件（通过函数提供）。(不改变原数组， 对空数组无效)
every() 方法使用指定函数检测数组中的所有元素：
- 如果数组中检测到有一个元素不满足，则整个表达式返回 false ，且剩余的元素不会再进行检测。
- 如果所有元素都满足条件，则返回 true。
```
var arr1 = [1, 2, 3];
var boolean = arr1.every(function(val, index, arr){
  console.log(`val:${val},index:${index},arr:${arr}`);
  return val < 4;
})
console.log(boolean);

// val:1,index:0,arr:1,2,3
// val:2,index:1,arr:1,2,3
// val:3,index:2,arr:1,2,3
// true
```

### `.some()`
与`.every()`方法类似，相当于`||`符号，只要有一个`true`则返回`true`，只有全部为`false`才返回`false`


### `.filter()`
`filter()`方法创建一个新的数组，新数组中的元素是通过检查指定数组中符合条件的所有元素。(不改变原数组， 对空数组无效)
```
var arr1 = [1, 2, 3];
var arr2 = arr1.filter(function(val, index, arr){
  console.log(`val:${val},index:${index},arr:${arr}`);
  return val > 1;
})
console.log(arr2);
//val:1,index:0,arr:1,2,3
// val:2,index:1,arr:1,2,3
// val:3,index:2,arr:1,2,3
// (2) [2, 3]
```

### `.forEach()`
这个方法从头至尾遍历数组，为每个元素调用指定的函数。操作的是原数组
```
var array = [1, 2, 3, 4];
var b = array.forEach(function (value, index, arr) {
  arr[index] = value + 1;
  console.log(`value:${value}, index:${index}, arr:${arr}`);
})
console.log(`array:${array},b:${b}`);
```

值得注意的是，`forEach()`方法**无法在所有元素都传递给调用的函数之前终止遍历。** 也就是说，不能提前终止，如果一定要终止，需要放在一个`try`块中，抛出`foreach.break`异常
```
function foreach(a, f, t){
  try{
    catch(e) {
      if (e === foreach.break) return;
      else throw e;
    }
  }
}
foreach.break = newError('StopIteration')
```

### `.map()`方法
和`forEach()`方法类似，但是调用这个方法可以返回一个新的数组，并不操作原数组。但是由于生成的是新数组，所有需要定义函数的返回值。
```
var arr1 = [1, 2, 3];
var arr2 = arr1.map((x) => {
  return x * x
})
```

## 归并方法`.reduce()`、`.reduceRight()`
合并方法，可以用来计算数组的总值。
```
var arr1 = [1, 2, 3];
var sum = arr1.reduce(function(first, second){
  return first + second;
})
console.log(sum); // 6
```

## 类数组对象
作为数组的字符串是不可变，只读的！！！

## ES6新特性
啊啊啊ES6特性实在太多，然后还有ES7 ES8 这个坑。。。慢慢来吧
