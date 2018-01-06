---
title: koa入门之文件上传
date: 2017-11-12 18:55:10
categories: 'koa'
tags:
---
写了那么多服务器代码，其实一直很少接触有关文件上传（文章附件下载功能）这方面的知识。以前还有一套express的实例看，最近转到Koa后，又遇到一个需要自己全权负责的项目。于是，自然得捡起来学啦~其实不麻烦，但是自己还是碰了好久的钉子
<!-- more -->
## 怎么实现附件下载
本来想法还比较幼稚，想着弄一个多弄一个字段来存储这方面信息。但是仔细想想，其实富文本编辑的时候，返回给前端显示的内容本来就可以动态插入`<a>`标签，那么干脆在处理时就把附件给转化为`<a>`标签即可。这样处理最为方便，但是有一个问题就是文件没办法实现删除？？这是一个暂时疑惑的点，明天和老哥讨论一下看看是怎样的

## 使用`koa-body`
官方支持的`koa-bodyParse`并不能实现文件的上传，于是，只能另找一个了。。本来还有的选择是`koa-multer`，但是稍微尝试了一下好像不太好用，而且感觉这样需要用两个组件来处理parse就有点繁杂，干脆直接另转一个中间件同时具备两个功能就好了。（还有一点`koa-multer`没有typescript的支持，要自己写，还是萌新地我本能的畏惧）

在[`koa-body`](https://github.com/dlau/koa-body)和`koa-beauty-body`之间抉择的时候，果断通过stars来判断到底哪个好=。=不过不得不说在文档方面对新人太不友好了一点。所以今天也是看了很多文章（其实没找到很多，相关教程并不多见），和官网的demo才终于踩完了这个坑。于是今天首先学到一点，在各种中间件的git主页，都会写一些examples给你参考。但是就是对新手不太友好，不过也是要习惯着看吧~~~

话不多说，直接给demo
```
import * as koaBody from 'koa-body';
app.use(koaBody({
  // 设置是否允许传递文件
  multipart: true,
  // 设置form表单的最大传输大小
  formLimit: '1000kb',
  // 有关form传递文件的更多配置
  formidable: {
    // 如果对文件名没有特殊要求，则直接利用这个键值就能实现文件的转储存
    // uploadDir: path(string)
    // 重命名文件并储存到静态文件夹中
    // 这里利用了onFileBegin的钩子函数对文件进行存储前的提前处理
    onFileBegin: (name, file) => {
      const fileName = `${getDateTime()}${file.name}`;
      file.path = `${__dirname}\\public\\${name}\\${fileName}`;
      file.name = fileName;
    }
  }
}));
```
定义完处理的中间键后，在post请求直接这样取值即可
```
export const test = async (ctx: Context) => {
  const fields = ctx.request.body.fields; // 表单中其他非文件的字段
  const files = ctx.request.body.files; // 表单中的文件字段（已经过中间键处理）
  console.log(files);
  ctx.body = JSON.stringify(ctx.request.body, null, 2);
};
```

没错，看起来就这么简单。但是由于网上的教程混合着koa1.0和koa2.0 以及各种原因，踩坑好久才终于搞懂了（不得不感叹，看不懂的时候死命琢磨官网才是正确的选择）

## 另外一个小坑，postman的设置
好像已经不止一次因为没运用好postman，导致出BUG了。使用文件上传功能，必须勾选`Body`为`form-data`，以及将`Headers`中的设置删除掉，不然就会出现奇奇怪怪的东西=。= 今天因为这个我以为自己代码写错了，搞了忒久了。。。。

## 后记一些
使用ts的时候，有一点限制的就是=。= 总要去找有ts支持的啊，毕竟没支持就要自己去写支持，虽然知道这是必然要学的部分，但是还是好怂啊~~~~~
然后还是要积极去多接触项目吧，项目推动学习需求，今天这个小功能虽然折腾我好久，但是以后有关文件上传的大概都懂啦~~~（前面还是留了个小坑，怎么处理已经没用的文件，明天补坑吧~）
