---
title: 细数js之实现继承
date: 2017-10-14 14:43:03
categories: 'js'
tags:
  - '细数js'
---
今天，来看一下经常不用的继承233（随着业务的复杂，以后肯定就不是这个说辞了）
<!-- more -->
继承是OO语言中的一个最为人津津乐道的概念。许多OO语言都支持两种继承方式：接口继承和实现继承。接口继承只继承方法签名，而实现继承则继承实际的方法。如前所述，由于函数没有签名，所以无法实现接口继承。而js中继承主要依靠原型链来实现。

## 1.原型链
原型链的基本思想是利用原型让一个引用类型继承另一个引用类型的属性和方法。也就是将每个对象的`prototype`指向另一个新类型的实例。实现的本质是重写原型对象，代之以一个新类型的实例。

```
Children.prototype = new Parent();
```
这里很明显很看出一个注意点。使用原型链继承时，不能使用对象字面量创建原型方法。
```
// 对象字面量
Children.prototype = {
  getValue: function(){}
}
```

### 原型链的问题
#### 问题1
主要的问题来自包含引用类型值的原型。在通过原型来实现继承时，原型实际上会变成另一个类型的实例。于是，原先的实例属性也就顺理成章地变成了现在的原型属性了。也就是说继承时，子类共享父类引用类型属性的值，这显然是不合理的。
```
function Parent() {
  this.colors = ['red', 'blue'];
}
function Children(){}
Children.prototype = new Parent();

var child = new Children();
var child2 = new Children();
child.colors.push('green');
console.log(child.colors); //["red", "blue", "green"]
console.log(child2.colors); //["red", "blue", "green"]
```
#### 问题2
在创建子类型的实例时，不能向超类型的构造函数中传递参数。实际上，应该说是没有办法在不影响所有对象实例的情况下，给超类型的构造函数传递参数。**所以，在实践中很少会单独使用原型链**

#### 总的来说
特点：
1. 非常纯粹的继承关系，实例是子类的实例，也是父类的实例
2. 父类新增原型方法/原型属性，子类都能访问到
3. 简单，易于实现
缺点：
1. 可以在子类构造函数中，为子类实例增加实例属性。如果要新增原型属性和方法，则必须放在new Parent()这样的语句之后执行。
2. 无法实现多继承
3. 来自原型对象的引用属性是所有实例共享的（详细请看附录代码： 示例1）
4. 创建子类实例时，无法向父类构造函数传参
推荐指数：★★（3、4两大致命缺陷）

## 2.借用构造继承
思想核心：借用父类的构造函数来增强子类的实例，相当于把父类的构造函数放到子类里面，复制父类的实例属性给子类。（没用到原型）。
```
function Parent(){
  this.name = 'parent';
}
function Children(){
  Parent.call(this);
}
var children = new Children();
console.log(children); //Children {name: "parent"}
console.log(children instanceof Parent); //false
console.log(children instanceof Children); //true
```
### 特点：
1. 解决了原型链中，子类实例共享父类引用类型属性的问题
2. 创建子类实例时，允许向父类传递参数
```
function Parent(name) {
  this.name = name;
}
function Children(){
  Parent.call(this, 'singlemai');
}
```
3. 可以实现多继承(借用多个父类的构造函数即可)

### 缺点：
1. 生成的实例并不是父类的实例，只是子类的实例。
2. 只能继承父类的实例属性和方法，不能继承原型属性/方法
3. 无法实现函数复用，每个子类都有父类实例函数的副本，影响性能。

## 3.组合继承
组合继承，就像名字所说，将原型链和借用构造函数两个方法借鉴组合，从而发挥二者之长。
**核心思想：使用原型链实现对原型属性和方法的继承，而通过借用构造函数来实现对实例属性的继承。**
```
function Parent(name) {
  this.name = name;
  this.colors = ['red', 'blue'];
}
Parent.prototype.sayName = function() {
  console.log(this.name);
  return
}
function Children(str = 'xixi') {
  Parent.call(this, 'children');
  this.child = str;
}
Children.prototype = new Parent();
Children.prototype.constructor = Parent;
Children.prototype.sayChild = function() {
  console.log(this.child);
  return
}
var child1 = new Children('haha');
child1.colors.push('green');
console.log(child1.colors);
child1.sayName();
child1.sayChild();
console.log('---');
var child2 = new Children();
child2.colors.push('pink');
console.log(child2.colors);
child2.sayName();
child2.sayChild();
```
### 特点
1. 弥补了方式2的缺陷，可以继承实例属性/方法，也可以继承原型属性/方法
2. 既是子类的实例，也是父类的实例
3. 不存在引用属性共享问题
4. 可传参
5. 函数可复用

## 4.寄生组合式继承
**核心思想：通过寄生方式，砍掉父类的实例属性，这样，在调用两次父类的构造的时候，就不会初始化两次实例方法/属性，避免的组合继承的缺点。** 文字描述有点难以表述，直接贴代码
```
function inheritPrototype(Children, Parent) {
  var Super = function(){};//构造一个空类
  Super.prototype = Parent.prototype;
  Children.prototype = new Super();
}
function Parent(){
  this.name = 'parent';
}
function Children(){
  this.child = 'child';
}
```
### 特点：
  1. 堪称完美
### 缺点：
  1. 实现较为复杂
推荐指数：★★★★（实现复杂，扣掉一颗星）

至此，所有实现方式都说完啦~~想起当时刚学js的时候来看这一段，完全一脸懵逼。现在看来，一下子便理解下来了。

---
参考文章:
[JS实现继承的几种方式](http://www.cnblogs.com/humin/p/4556820.html)
