---
title: URL和URI和RUN这三个能否区分？
date: 2017-09-29 10:37:42
categories: 'html+css'
---
这个应该是一个简短的科普文~~学习就是不断地排查自己不懂的内容然后记住它！所以如果你不知道URL URI URN，那么看一下!
<!-- more -->
我认为，尽管对一般人来说，不了解这三个缩略词之间的技术差异以及它们各自的含义并不是什么问题。但是，如果你作为一个计算机科学家、一个Web开发者、一个系统管理员，或者更笼统地说，你工作在IT领域，那么了解这些知识就非常有必要了。

## URI
统一资源标识符（URI）提供了一个简单、可扩展的资源标识方式。URI规范中的语义和语法来源于万维网全球信息主动引入的概念，万维网从1990年起使用这种标识符数据，并被描述为“万维网中的统一资源描述符”。

URL 和 URN 都是 URI 的子集。也就是说URL和URN都是URI，但是URI不一定是URL或者URN。

## 那么URL 和 URN 又是啥
关于URL：
> URL是URI的一种，不仅标识了Web 资源，还指定了操作或者获取方式，**同时指出了主要访问机制和网络位置。**

关于URN：
> URN是URI的一种，用特定命名空间的名字标识资源。使用URN可以在不知道其网络位置及访问方式的情况下讨论资源。

所有的URN都遵循如下语法（引号内的短语是必须的）：
```
< URN > ::= "urn:" < NID > ":" < NSS >
```
其中NID是命名空间标识符，NSS是标识命名空间的特定字符串。

直接看个例子区分：
URI:
```
http://bitpoetry.io/posts/hello.html#intro
```
资源：
```
#intro
```
URL：
```
http://bitpoetry.io/posts/hello.html
```
URN:
```
bitpoetry.io/posts/hello.html#intro
```

嗯呢，就这么简单~

---
参考文章：
[你知道URL、URI和URN三者之间的区别吗？](http://mp.weixin.qq.com/s/9Q4dE97eaUrUTjmR0qLOmQ)
