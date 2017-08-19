---
title: HTML5--history对象
date: 2017-06-03 16:06:43
categories: 'html+css'
tags:
---
HTML5 history
<!-- more -->
## History对象
主要是用于控制浏览器的历史记录，包括前进后退、修改历史记录等。
我觉得可以利用在单页面，控制不能回退操作页面。
## 使用History
HTML5提供了两个新的方法(**history.pushState()**/**history.replaceState()**)，用于添加和更新历史记录，还有一个**popstate**事件。

### history.pushState(state,title,url)
#### 参数
state: 存储JSON字符串，可以用在popstate事件中。 记录历史记录点的额外对象，可以为空。     
title: 大多是浏览器不支持，建议使用null。      
url:任意有效的URL，用于更新浏览器的地址栏，不在乎url地址是否存在，只改变url，url必须同域。更重要的是，**它不会重新加载页面。**      

#### 作用
pushState()是在history栈中添加一个新的条目，也就是添加多一条的历史记录。也就是说使用pushState(),会使历史记录条数+1

### history.replaceState(state,title,url)
可以看出这个方法接收的参数和pushState一致。它的区别在于，这个方法会将原本在栈的第一条数据替换成新传入的url。历史记录条数不变。

### popstate事件（window.onpopstate)
触发popstate事件的条件：浏览会话历史记录
1. 点击前进或者后退按钮
2. 使用history.go() 或者 history.back()方法

### 错误解决
```
Uncaught SecurityError: Failed to execute 'replaceState' on 'History': A history state object with URL 'file:///C:/Users/athite/Desktop/DEMO/page.html' cannot be created in a document with origin 'null'.**
```
这个错误是因为没有开启服务器！！！！！！！！！！！还有可能是因为jq的影响。一开始没意思到，还浪费了很多时间

### Demo示例
注意使用IIS建站测试
HTML:
```
<div>
    <div>
        <ul>
            <li><a href="home.html" class="historyAPI">页面1</a></li>
            <li><a href="about.html" class="historyAPI">页面2</a></li>
            <li><a href="contact.html" class="historyAPI">页面3</a></li>
        </ul>
    </div>
    <div class="row">
        <div class="col-md-6">
            <div class="well">
                点击上方的链接，触发pushState方法。
            </div>
        </div>
        <div class="row">
            <div id="contentHolder">
                <h1>主页面</h1>
            </div>
        </div>
    </div>
</div>
```
#### demo1
JavaScript:
```
<script type="text/javascript" src="http://code.jquery.com/jquery-latest.js"></script>
    <script type="text/javascript">
        $('document').ready(function(){

            $('.historyAPI').on('click', function(e){
                e.preventDefault();
                var href = $(this).attr('href');

                // 获取地址页面内容
                getContent(href, true);

                $('.historyAPI').removeClass('active');
                $(this).addClass('active');
            });

        });

     // 监听popstate事件，也就是监听浏览器前进后退按钮。
     // 尝试注释这段代码，会发现浏览器地址栏地址已经改变，但是页面并不刷新。
    window.addEventListener("popstate", function(e) {
        // 触发更新页面数据
        getContent(location.pathname, false);
    });

        // addEntry 用于判断是否触发popstate事件而产生的页面内容变更
        // false: 是由点击事件产生的页面变更，需要添加历史记录到history中
        // true: 是由popstate事件产生的页面变更，不需要变更历史记录
    function getContent(url, addEntry) {
        $.get(url)
            .done(function( data ) {
                // 将地址页面内容更新至当前页面
                $('#contentHolder').html(data);

                if(addEntry == true) {
                    // Add History Entry using pushState
                    history.pushState(null, null, url);
                }
            });
    }
</script>
```
##### 实现效果
页面不刷新，可以使用前进后退按钮进行跳转页面。

#### demo2
注释掉上面的javascript，插入这段 变成触发replaceState方法
```
<script>
        $('document').ready(function(){
            $('.historyAPI').on('click',function (e) {
                e.preventDefault();
                var href = $(this).attr('href');
                // 获取页面地址内容
                getContent(href);

                $('.historyAPI').removeClass('active');
                $('this').addClass('active');
            })
        });
        function getContent(url) {
            $.get(url)
                .done(function (data) {
                    $('#contentHolder').html(data);
                    // 每次更换都替换掉当前的浏览记录，也就是保持没有增加历史记录
                    // 使浏览器不能回退
                    history.replaceState(null,null,url);
                })
        }
    </script>
```

链接：http://blog.jobbole.com/78876/
