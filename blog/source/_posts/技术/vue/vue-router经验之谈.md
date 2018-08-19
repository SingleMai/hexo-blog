---
title: vue-router经验之谈
date: 2018-08-19 10:52:38
categories: Vue
tags:
---

记录一下学习vue-router以来学到的坑。
<!-- more -->

`vue-router`在匹配路由的时候

```js
/user/bar
/user/foo
```
都会被匹配到`/user/:params/`下，这时候如果在`/user/bar`切换到`/user/foo`时，因为为了优化渲染，将会复用该组件。造成的影响是，该组件将不会被调用`create/mounted`等生命周期钩子函数。

于是想要监听此类操作，则需要在`watch`函数中，监听`$route`

```js
const User = {
  template: '...',
  watch: {
    '$route' (to, from) {
      // 对路由变化作出响应...
    }
  }
}
```
或者利用`beforeRouteUpdate()`钩子函数。

```js
const User = {
  template: '...',
  beforeRouteUpdate (to, from, next) {
    // react to route changes...
    // don't forget to call next()
  }
}
```

这个应用场景就有如，当前页面在自己的首页，`/user/1`。这时候你点击关注你的那位小伙伴切换到他的首页去,`/user/2`。

## 子路由的嵌套

```js
const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User,
      children: [
        {
          // 当 /user/:id/profile 匹配成功，
          // UserProfile 会被渲染在 User 的 <router-view> 中
          path: 'profile',
          component: UserProfile
        },
        {
          // 当 /user/:id/posts 匹配成功
          // UserPosts 会被渲染在 User 的 <router-view> 中
          path: 'posts',
          component: UserPosts
        }
      ]
    }
  ]
})
```

这里的子路由嵌套，需要注意的时，如果定义在子路由中，进行取代渲染的内容是子组件内的`router-view`。而不是父组件的`router-view`

## `router.push(location, onComplete?, onAbort?)`
这个方法相当于向浏览器的历史记录中推入了一条记录。效果和`<router-link></router-link>`标签一致。所以这种情况下的切换，当用户点击回退按钮的时候，将会回退到上一个页面。

```js
// 字符串
router.push('home')

// 对象
router.push({ path: 'home' })

// 命名的路由
router.push({ name: 'user', params: { userId: 123 }})

// 带查询参数，变成 /register?plan=private
router.push({ path: 'register', query: { plan: 'private' }})
```
**注意，如果提供了`path`，那么`params`则会被忽略。而`query`则不会受这个影响。如果非要使用`params`不可的话，则需要在定义路由的时候赋予一个`name`。或者手填组成该路由**

```js
const userId = 123
router.push({ name: 'user', params: { userId }}) // -> /user/123
router.push({ path: `/user/${userId}` }) // -> /user/123
// 这里的 params 不生效
router.push({ path: '/user', params: { userId }}) // -> /user
```

这里第二`onComplete`第三`onAbort`个参数是提供的回调。这些回调将会在导航成功完成 (在所有的异步钩子被解析之后)或终止(导航到相同的路由、或在当前导航完成之前导航到另一个不同的路由)的时候进行相应的调用。

## `router.replace(location, ?onComplete, ?onAbort)`
这个方法与`router.push()`几乎一致，唯一不同的是。将会替换掉当前的历史记录，而不是插入

## `router.go(n)`
相当于`window.history.go(n)`完全一样的用法.

## `router.redirect()`
这里有一点需要特别注意：`导航守卫`并没有应用在跳转路由上，而仅仅应用在其目标上。在下面这个例子中，为 /a 路由添加一个 `beforeEach` 或 `beforeLeave` 守卫并不会有任何效果。

## 别名
『重定向』的意思是，当用户访问 `/a`时，URL 将会被替换成 `/b`，然后匹配路由为 `/b`，那么『别名』又是什么呢？

`/a` 的别名是 `/b`，意味着，当用户访问 `/b` 时，URL 会保持为 `/b`，但是路由匹配则为 `/a`，就像用户访问 `/a` 一样。

上面对应的路由配置为：

```js
const router = new VueRouter({
  routes: [
    { path: '/a', component: A, alias: '/b' }
  ]
})
```

这个功能简直了。让你可以自由地将 UI 结构映射到任意的 URL，而不是受限于配置的嵌套路由结

## 导航守卫
可以理解为一系列钩子函数。初略看了一下，感觉不知道在什么情况下怎么使用。所以粗略的带过吧。等以后再回头？

## 路由元信息
`meta`字段，同样没有想象到应用场景，略

## 过渡特效
应该是完全和vue的过渡效果一致，有一点比较特殊的吧。动态判断路由特效。这里通过路径的长短来划分左滑或者右滑。还是挺新鲜的233

```js
<!-- 使用动态的 transition name -->
<transition :name="transitionName">
  <router-view></router-view>
</transition>
// 接着在父组件内
// watch $route 决定使用哪种过渡
watch: {
  '$route' (to, from) {
    const toDepth = to.path.split('/').length
    const fromDepth = from.path.split('/').length
    this.transitionName = toDepth < fromDepth ? 'slide-right' : 'slide-left'
  }
}
```

## 滚动行为
使用前端路由，当切换到新路由时，想要页面滚到顶部，或者是保持原先的滚动位置，就像重新加载页面那样。 `vue-router` 能做到，而且更好，它让你可以自定义路由切换时页面如何滚动。

这里有应用场景，但是不是特别的需求应该都用不到，所以也只做mark一下吧。算是优化体验的一种。今天还看到另一种提到的方法。在组件内监听滑动事件，动态记录滑动的位置存进`localstorage`中，下次进入时直接滑动到所需的位置，感觉也不错。就是不知道和这个方法有何区别。感性上的认识，这个路由方法应该不能解决页面刷新的问题？毕竟所有的数据都会消失

## 路由懒加载
和组件懒加载一致

## 如何处理404页面
这里讨论`404`页面就有必要讨论一下`model:hast/history`了。一般来说，这只是`vue-router`在浏览器上地址的变化。
- 一个带有`#`，一个不带。
- 一个只需要服务器处理一个路由。一个需要服务器全面覆盖路由。

这篇文章有所介绍[vue-router的两种模式的区别](https://juejin.im/post/5a61908c6fb9a01c9064f20a)
### hash模式
在路由路径下，添加一个

```js
{
  path: '*',
  conponent: Error // Error404组件
}
```
匹配所有没被定义的路由。

### history模式


至此，文档入门教学部分已经结束。具体API文档需要更多时间去看吧~~