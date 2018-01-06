---
title: Vue非父子组件的通信
date: 2017-12-29 23:33:00
categories: Vue
tags:
---
在刚学vue的时候,那时候就被告知.vue只存在父与子进行通信.同级组件请用vuex.但是毫无疑问,对于一个很小的项目来说,使用vuex简直就是杀鸡用牛刀.十分的不好使.但是当时又没找到更好的解决方案.=.= 终于看到了解决方案的文章呀~~
<!-- more -->

## 非父子组件的通信
在非父子组件中,可以利用**集中式的事件中间件Bus**进行事件的派发.按照建议,将`Bus`挂载在全局下
```
//app.js
var eventBus = {
    install(Vue,options) {
        Vue.prototype.$bus = vue
    }
};
Vue.use(eventBus);
```
然后在组件中，可以使用$emit， $on， $off 分别来分发、监听、取消监听事件：
- 分发事件的组件
    ```
    //
    methods: {
      todo: function () {
        this.$bus.$emit('todoSth', params);  //params是传递的参数
        //...
      }
    }
    ```
- 监听组件
    ```
    // ...
        created() {
          this.$bus.$on('todoSth', (params) => {  //获取传递的参数并进行操作
              //todo something
          })
        },
        // 一定要在组件销毁前
        // 清除事件监听
        beforeDestroy () {
          this.$bus.$off('todoSth');
        },
    ```

按照这里的格式.有一个事件,就必须写一次=.= 所以很明显,当这个状态管理复杂起来的时候,这个是很脆弱的..甚至复杂到根本不能管理.所以只是适用在小项目中只有一点点事件的时候用啦.

需要注意的点  
1. 在监听组件中,销毁组件时必须将事件解绑=.=
2. 项目复杂起来请用vuex

---
[Vue 组件通信之 Bus](https://juejin.im/post/5a4353766fb9a044fb080927)
[tangdaohai/vue-happy-bus](https://github.com/tangdaohai/vue-happy-bus)
