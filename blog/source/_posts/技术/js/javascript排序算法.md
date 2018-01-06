---
title: javascript排序算法
date: 2017-09-18 14:43:03
categories: 'js'
---
hmm，虽说感觉算法在实际工作中总是不常见。但是，不得不承认算法才是程序的核心。所以还是有必要学习的。
<!-- more -->
## 排序算法
大一学习排序的时候就只学了两种最简单的，然而居然还是有点忘了，试验了很多才终于写出一个无bug版的。可见自己算法能力多低下了。
### 冒泡排序
依次比较相邻的两个元素，如果后一个小于前一个，则交换，这样从头到尾一次，就将最大的放到了末尾。

从头到尾再来一次，由于每进行一轮，最后的都已经是最大的了，因此后一轮需要比较次数可以比上一次少一个。虽然你还是可以让他从头到尾来比较，但是后面的比较是没有意义的无用功，为了效率，你应该对代码进行优化。
```
  // 冒泡算法
  // function sortArray(oriArr) {
  //   var temp;
  //   var _arr = oriArr;
  //   for (let i = 0; i < oriArr.length - 1; i++) {
  //     for (let j = 0; j < oriArr.length -1 - i; j++) {
  //       if (_arr[j] > _arr[j + 1]) {
  //         temp = _arr[j];
  //         _arr[j] = _arr[j + 1];
  //         _arr[j + 1] = temp;
  //       }
  //     }
  //   }
  //   return _arr;
  // }
```

### 选择排序
选择排序大概也是思路最简单的了。
1. 在未排序数组里面找到最大或者最小时，插入
2. 再从未排序的剩余数组找到最大或者最小，插入已排序的末尾
3. 重复，直到排序完毕

```
function sortArray(oriArr) {
  var temp;
  var _arr = oriArr;
  var min, pos;
  for (let i =0; i < _arr.length - 1; i++) {
    min = _arr[i];
    pos = i;
    for (let j = i + 1; j < _arr.length; j++) {
      if (min > _arr[j]) {
        min = _arr[j];
        pos = j;
      }
    }
    temp = _arr[i];
    _arr[i] = min;
    _arr[pos] = temp;
  }
  return _arr;
}
```

### 简单的代价就是低效
上面的方法都是低效的排序方法。而且复杂度是一样。。而且相比来说冒泡排序还相对稳定，而选择排序要不稳定。所以简单的排序使用冒泡会更合理。

那么，说好的算法篇，肯定得想办法优化啊~~

### 归并排序法
归并排序虽然不是空间复杂最简单的，但是相比冒泡这种要进步不少。（当然必须复杂不少）所以我们先学学这个。

基本的原理就是，分治，分开并递归排序。

1. 申请空间，使其大小为两个已经排序序列之和，该空间用来存放合并后的序列；
2. 设定两个指针，最初位置分别为两个已经排序序列的起始位置；
3. 比较两个指针所指向的元素，选择相对小的元素放入到合并空间，并移动指针到下一位置；
4. 重复步骤 3 直到某一指针达到序列尾；
5. 将另一序列剩下的所有元素直接复制到合并序列尾。

形象点解释就是将一个x长度的数组，利用递归平均分开成两份（不追求绝对平均）直到每一份都只有两个数组的比较。然后，利用只需要比较两个数的大小进行插入操作。可能还是有点绕，举个demo

原始数组： [1,4,3,2,5,6];
递归拆分第一次： [1,4,3] [2,5,6]
递归拆分第二次： [1,4] [3] [2,5] [6]
现在已经是长度为2了，所以可以排序了
递归排序开始： [1,4] [3] [2,5] [6]
递归排序第二次： [1,3,4] [2,5,6]
递归排序第三次： [1,2,3,4,5,6]

由于进入递归排序的那两个数组都是已经排序好了的，所以你只需要对比它们相同index的值然后排序即可

```
function mergeSort(arr) {  // 采用自上而下的递归方法
   var len = arr.length;
   if(len < 2) {
       return arr;
   }
   var middle = Math.floor(len / 2),
       left = arr.slice(0, middle),
       right = arr.slice(middle);
   return merge(mergeSort(left), mergeSort(right));
}

function merge(left, right)
{
   var result = [];

   while (left.length && right.length) {
       if (left[0] <= right[0]) {
           result.push(left.shift());
       } else {
           result.push(right.shift());
       }
   }

   while (left.length)
       result.push(left.shift());

   while (right.length)
       result.push(right.shift());

   return result;
}
```

这里再贴一段大神写的，可以根据对象中属性进行排序的。mark一下，试着自己实现吧
```

/**
* [归并排序]
* @param  {[Array]} arr   [要排序的数组]
* @param  {[String]} prop  [排序字段，用于数组成员是对象时，按照其某个属性进行排序，简单数组直接排序忽略此参数]
* @param  {[String]} order [排序方式 省略或asc为升序 否则降序]
* @return {[Array]}       [排序后数组，新数组，并非在原数组上的修改]
*/
var mergeSort = (function() {
   // 合并
   var _merge = function(left, right, prop) {
       var result = [];

       // 对数组内成员的某个属性排序
       if (prop) {
           while (left.length && right.length) {
               if (left[0][prop] <= right[0][prop]) {
                   result.push(left.shift());
               } else {
                   result.push(right.shift());
               }
           }
       } else {
           // 数组成员直接排序
           while (left.length && right.length) {
               if (left[0] <= right[0]) {
                   result.push(left.shift());
               } else {
                   result.push(right.shift());
               }
           }
       }

       while (left.length)
           result.push(left.shift());

       while (right.length)
           result.push(right.shift());

       return result;
   };

   var _mergeSort = function(arr, prop) { // 采用自上而下的递归方法
       var len = arr.length;
       if (len < 2) {
           return arr;
       }
       var middle = Math.floor(len / 2),
           left = arr.slice(0, middle),
           right = arr.slice(middle);
       return _merge(_mergeSort(left, prop), _mergeSort(right, prop), prop);
   };

   return function(arr, prop, order) {
       var result = _mergeSort(arr, prop);
       if (!order || order.toLowerCase() === 'asc') {
           // 升序
           return result;
       } else {
           // 降序
           var _ = [];
           result.forEach(function(item) {
               _.unshift(item);
           });
           return _;
       }
   };
})();
```
---
参考：
[JavaScript排序，不只是冒泡](http://mp.weixin.qq.com/s/59wrBe4Yxw1r9wzGIon07A)
