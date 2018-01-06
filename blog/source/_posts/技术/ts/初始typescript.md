---
title: 初识typescript
date: 2017-10-09 15:53:58
categories: ts
---
其实吧，一直觉得ts是可望不可及的~然而项目要求要用到。那么没办法，硬着头皮也得学，233
<!-- more -->
这一篇没有太多的技术点。更多是说说自己对ts的认识和前期的配置。说白了，就是给自己看的文章而已。

首先，学习ts还是秉承着我一直以来的习惯先找了个视频来初步入门~~接口，类的继承等等特性，无一不让我觉得完全回归到当初学习java的时代。于是**JavaScript 变成了真正意义上的 Java + script**.由于习惯了类型的不确定，第一感觉是握草，尼玛真麻烦，看代码看到头都晕了。

奈何，连入门都还不算的我已经要准备开发了。所以就先在atom上装上了`atom-typescript`插件,然后再不断根据它的新要求（它还依赖了很多插件）一股脑下载下来，发现原来可爱的atom突然变成了高大上的ide，稍微写了一段，“啊啊啊啊，熟悉的报错界面”。于是便理解了，拥有了ts，仿佛就要少了很多检查拼写等等诸如此类傻逼的犯错（debug的能力一下子拔高了好多的说）

当然这篇文章，其实更多是想mark一下`tsconfig.json`这个配置文件。自己还不太会配，稍微网上找了几段，同样作mark用。
[TypeScript tsconfig.json](https://www.w3cschool.cn/typescript/typescript-tsconfig-json.html)
[TypeScript编译选项](https://github.com/zhongsp/TypeScript/blob/master/doc/handbook/Compiler%20Options.md)
[使用Typescript开发node.js项目——简单的环境配置](https://segmentfault.com/a/1190000007574276)

-----
更新于10.18=。= 入门需要很快地过了一遍[TypeScript 入门教程](https://ts.xcatliu.com/)。于是就稍微打消了基础类的博客了。以后看看会不会深入学习，才考虑这个系列是否会继续吧~

-----

然后自己稍微配了一下下，其实没有完全吃透，但是感觉吃透这个配置文件不是现在需要做的事情。所以mark一下就算啦
```
{
  "compileOnSave": true,
  "compilerOptions": {
      "module": "commonjs",
      "target": "es6",
      "noImplicitAny": true,
      "sourceMap": false,
      "rootDir":"./",
      "outDir":"../js",
      "watch":true
  },
  "include":[
      "./**/*"
  ]
}
```
