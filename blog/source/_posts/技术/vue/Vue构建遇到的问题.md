---
title: Vue构建遇到的问题
date: 2017-11-15 18:34:10
categories: 工具配置
tags:
---
这将是一篇杂文，汇总自己开发过程中遇到的所有有关vue构建的问题。
<!-- more -->
## 解决Error: ENOENT: no such file or directory, scandir 'D:\IdeaWork\code-front-jet\node_modules\.npmins
在使用npm安装`node-sass`的时候，可能会出现如下的报错：
```
Error: ENOENT: no such file or directory, scandir 'D:\IdeaWork\code-front-jet\node_modules\.npminstall\node-sass\3.7.0\node-sass\vendor'

    at Error (native)
```
解决方案是执行以下方法：
`npm rebuild node-sass`

当然，我也遇到了不属于`node-sass`的报错。是另一个奇怪的报错。这时候
```
npm install -g npm@latest
```
更新`npm`版本就可以了=。= 具体原因不明

## 引入ts
[How to remove experimentalDecorators warning in VSCode](https://ihatetomatoes.net/how-to-remove-experimentaldecorators-warning-in-vscode/)
[TypeScript 支持](https://cn.vuejs.org/v2/guide/typescript.html)
[Vue2.5+ Typescript 引入全面指南](https://segmentfault.com/a/1190000011853167#articleHeader5)
[vue-class-component](https://github.com/vuejs/vue-class-component)

取消掉`experimentalDecorators`的警告：
```
// tsconfig.json
"compilerOptions": {
  // 取消掉警告
  "experimentalDecorators": true,
  // 允许引入没有types申明的js文件
  "allowJS": true
}

```

### 在app.ts文件下会导致new App()但是判断为未使用
```
// tslint.json
// 允许定义new 未使用
"rules": {
  "no-unused-expression": [true, "allow-new"]
}
```

### 解决 Webpack "Invalid Host Header"
原因具体可以看文章：也就是webpack的安全保护
[解决 Webpack "Invalid Host Header"](http://blog.csdn.net/salmonellavaccine/article/details/75332654)
```
// webpack.dev.conf.js
devServer: {
  // 禁用主机检测
  disableHostCheck: true,
}
```

### vue+ts .vue文件import报错
只要在src根目录下，新建一个`vue-shim.d.ts`文件，里面内容如下：
```
// 告诉TypeScript `*.vue`后缀的文件可以交给`vue`模块来处理
declare module "*.vue" {
    import Vue from 'vue'
    export default Vue
}
```

### 如何编写.d.ts文件
[如何编写一个d.ts文件](https://segmentfault.com/a/1190000009247663)

### 扩展支持Object.assign
```
declare interface ObjectConstructor {
    assign(target: any, ...sources: any[]): any;
}
```

### Vue v-for 报错
[vetur插件提示 [vue-language-server] Elements in iteration expect to have 'v-bind:key' directives错误的解决办法](http://blog.csdn.net/xufangfang99/article/details/77882792)

### 引入sass
[如何在vue-cli中引入sass](https://www.cnblogs.com/rainheader/p/6505366.html)
