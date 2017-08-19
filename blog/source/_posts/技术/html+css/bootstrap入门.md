---
title: bootstrap入门
date: 2017-05-16 21:48:21
categories: "html+css"
tags:
    - jq
    - bootstrap
---
想想自己最近做的项目或多或少都在使用bootstrap,但是自己对bootstrap很多知识还是有漏洞的，不全面。于是，趁着今天稍有空闲。迅速过了一遍入门篇的视频，整理笔记如下。
<!-- more -->
bootstrap是基于JQuery的，所以使用bootstarp的时候要注意先引入JQuery（并且注意版本问题）。**Jetstrap** 是基于bootstrap的开发工具，现在还没用，但是记录备用吧。

## 入门知识
### 标题
bootstrap对h1-h6的标签样式都进行了重写。可以直接使用标签，也可以在class类里面使用`.h1`来实现标题效果。另外，副标题的标签是small，class是`.small`

### 排版
`.text-left`、`.text-right`、`.text-center`来定位文字的位置
`<mark>、<del>、<ins>、<u>、<strong>` 等一系列文本标签
`.text-lowercase`、`.text-upercase`、`.text-capitalize`定义字母大小写的类。

### 表格 table
带边框表格：`.table-bordered`
带条纹表格：`.table-striped`
悬停变色：`.table-hover`
紧凑表格：`.table-condensed`

### 表单 form
`.form-control`定义表单
`.form-group`表单组件化（一个input)
`.form-inline` 水平排列
`placeholder`输入框提示信息
`.input-large`大输入框
`.input-sm`小输入框
`.control-label`控制输入框的样式
  - `.has-success`
  - `.has-warning`
  - `.has-info`
  - `.has-error`

### 按钮
`.btn` 定义按钮
`.btn-default` 按钮默认样式
`.btn-success` 绿色按钮样式（成功）
`.btn-primary`
`.btn-info`
`.btn-warning`
`.btn-danger`
`.btn-link` 将按钮变成a标签样式
`.btn-lg/sm`控制大小
`.active`按下状态
`·disabled = 'disabled'` 禁用状态
`.btn-block` 占满父元素的按钮
`.btn-class` 添加在a标签中，可是使a标签展示按钮效果

### 图片
`.img-rounded` 圆角
`.img-circle` 圆形
`.img-thumbnail`带边框的圆角图形

### 辅助类
1. 文本颜色
2. 背景颜色
3. 状态设置
4. 三角符号

## 渐进——响应式、栅格、图标
### 响应式开发
meta标签中的viewport——`width/height/user-scalable(能否伸缩)/initial-scale=1(默认打开大小)/maximum-scale=1/minimum-scale=1`

手机端开发问题：在苹果手机网页显示的线条边框会更粗如何解决。
- 利用js来判断手机屏幕是不是视网膜屏（isRetina)如果是true，则将initial-scale设置为0.5，再动态插入meta标签来实现两者兼容。
```
var metaElement = doc.createElement('meta');
var scale = isRetina?0.5:1;
// 继续设置其他值插入html
```
响应式特点：
  1. 视野开阔，信息量丰富
  2. 排版新颖随意、设计师发挥空间大
  3. 适用PC和手机端

响应式开发主要是利用`@media screen and (min-width:* px) and (max-width:*px){}`来设置。而bootstrap也是基于此进行设置封装在类当中。

### 栅格化：
`.col-lg-12` 超大屏  >=1200
`.col-md-12` 正常屏幕 >= 992
`.col-sm-12` 小屏幕  >= 768
`.col-xs-12` 超小屏幕 <768
`.col-lg-offset-3` 重置偏移，向右侧偏移，增加左侧的边距。

### 单位
1. px 基于屏幕分辨率的单位
2. em
    - 1em = 16px（不完全）在各色系统和浏览器表现不同
    - em会继承父级元素的字体代销
3. rem
    - 继承根级元素字体大小
    - 所以可以设置`html{font-size:62.5%}`这样可以使得10px = 1rem.

### 字体图标
```
@font-size{
  font-family:''
  src:url('/url') format('')
}
```
图标库：glyphicon、阿里图标

## 组件
怪异属性：
  1. role 盲文浏览器
  1. aria-label 读屏软件
  1. tabIndex 焦点移动
  1. data- 自定义属性，实现数据传输

### 下拉菜单
1. `.dropdown`控制组件下拉
2. `.dropdown-menu-right`下拉向右对齐
3. `.divider`分割线
```
<div class="dropdown">
  <button class="btn btn-default dropdown-toggle" data-toggle="dropdown">
    <ul class dropdown-menu>
      <li></li>
      <li></li>
      <li></li>
    </ul>
  </button>
</div>
```

### 控件组
`.input-group` 表示控件组
`.input-group-addon` 可放置额外内容及图标
```
<div class="input-group">
  <span calss="input-group-addon">$</span>
  <input type="text" class="form-control">
</div>
```

### 导航
`.nav` 无序列表
`.nav-tabs` 可切换导航
`.nav-pills` 胶囊导航
`.nav-justified/stacked` 垂直导航
使用`.active`表示选择状态的导航条

### 分页
`.pagination` 在父级元素添加表示分页
`.pager` 放置翻页区域
`.previous` 链接左对齐
`.next` 链接右对齐
```
<ul class="pager"></ul>
<ul class="pagination">
  <li></li>
  <li></li>
  <li></li>
</ul>
```

### 进度条
`.progress` 表示进度条
添加状态类来改变颜色
`.progress-bar-stripped` 渐变
```
<div class="progress">
  <div class="progress-bar progress-bar-success progress-bar-stripped" sytle="width:60%"></div>
</div>
```
### 列表
`.list-group` 列表组
`.badge` 状态组
`.active` 选中
`.disabled` 禁选
```
<ul class="list-group">
  <li class="list-group-item">
    <span></span>
  </li>
</ul>
```

### 面板
`.panel` 面板
`.panel-body` 面板内容
`.panel-footer` 注脚
```
<div class="panel panel-default">
  <div class="panel-heading"></div>
  <div class="panel-body"></div>
  <div class="panel-footer"></div>
</div>
```

### 插件引用 弹窗（modal)
`data-toggle=modal`//使用弹窗
'data-target="#signupModal"'指向弹出的弹窗id
```
//jade
p.navbar-text.navbar-right
  a.navbar-link(href="#",data-toggle="modal",data-target="#signupModal") 注册
  span &nbsp;|&nbsp;
  a.navbar-link(href="#",data-toggle="modal",data-target="#signinModal") 登录

#signupModal.modal.fade
.modal-dialog
.modal-content
form(method="POST",action="/user/signup")
  .modal-header 注册
  .modal-body
    .form-group
      label(for="signupName") 用户名
      input#signupName.form-control(name="user[name]",type="text")
    .form-group
      label(for="signupPassword") 密码
      input#signupPassword.form-control(name="user[password]",type="text")
    .modal-footer
      button.btn.btn-default(type="button",data-dismiss="modal") 关闭
      button.btn.btn-success(type="sumbit") 提交

#signinModal.modal.fade
.modal-dialog
.modal-content
form(method="POST",action="/user/signin")
  .modal-header 登录
  .modal-body
    .form-group
      label(for="signinName") 用户名
      input#signinName.form-control(name="user[name]",type="text")
    .form-group
      label(for="signupPassword") 密码
      input#signinPassword.form-control(name="user[password]",type="text")
    .modal-footer
      button.btn.btn-default(type="button",data-dismiss="modal") 关闭
      button.btn.btn-success(type="sumbit") 提交
```
