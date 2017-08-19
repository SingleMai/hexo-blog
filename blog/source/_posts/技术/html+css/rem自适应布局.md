---
title: rem自适应布局
date: 2017-08-17 00:16:11
categories: "html+css"
---
rem布局，迅雷小哥疯狂给我安利，我也是吃得稳稳的。
<!-- more -->
rem的好处到处文章都是，大概的意思就是动态改变计算rem的值，然后自适应屏幕宽高，实现等比缩放。比百分比更好用，可以避免一些百分比引起的错误。所以精准布局可以使用rem，但是自适应宽高变化的话百分比还是有相对优势的。毕竟如果使用rem定大小，父级的容器改变不会影响子级，也就意味着每次修改都需要一连串的修改。而使用百分比则可以做到改父级连锁反应修改子级内容。

优劣暂时讨论这么多。直接贴rem代码

### 补充的注意点
引用rem的时候，需要将rem的计算代码放在所有样式的前面。确保rem先被计算完成，再进行布局。可以避免页面的卡顿，一瞬间的乱码。

或者用媒体查询（但是适配度低）

### js实现
自执行计算文字大小，这段代码适用于只有横屏或只有竖屏需求。只计算一个。
```
(function (win, doc) {
        if (!win.addEventListener) return;
        var html = document.documentElement;

        function setFont() {
            var cliWidth = html.clientWidth;
            html.style.fontSize = 100 * (cliWidth / 750) + 'px'; // 修改这里的750 根据psd的大小来修改
        }

        win.addEventListener('resize', setFont, false);
        doc.addEventListener('DOMContentLoaded', setFont, false);
    })(window, document);
```
这段适合横竖屏，但是在电脑端因为没有这个横竖屏的属性，所以调试的时候需要手动修改。
```

//rem
    var psdWidth = 640; // 测试用的修改
    function orient() {
        if (window.orientation == 90 || window.orientation == -90) {
    //ipad、iphone竖屏；Andriod横屏
            psdWidth = 1136;
            return false;
        }
        else if (window.orientation == 0 || window.orientation == 180) {
    //ipad、iphone横屏；Andriod竖屏
            psdWidth = 640;
            return false;
        }
    };
    window.addEventListener('orientationchange', function(e){
        orient();
        setFont();
    });

    function setFont() {
        var _cliWidth = window.innerWidth;
        var _fontSize = 100 * (_cliWidth/psdWidth)+'px';
        document.querySelector('html').setAttribute('font-size',_fontSize);
    }
    orient();
    setFont();
    window.addEventListener('resize',function () {
        orient();
        setFont();
    })

```

### CSS实现
这个方法就很粗暴，直接列明了是怎么实现的。也就是改变body标签的字体大小。js的方法其实也就这样，但是更精准。因为不是写死的
```
/* rem*/

@media only screen and (max-width: 1080px), only screen and (max-device-width:1080px) {
    html,body {
        font-size:144px;
    }
}
@media only screen and (max-width: 960px), only screen and (max-device-width:960px) {
    html,body {
        font-size:128px;
    }
}
@media only screen and (max-width: 800px), only screen and (max-device-width:800px) {
    html,body {
        font-size:106.66666666666667px;
    }
}
@media only screen and (max-width: 720px), only screen and (max-device-width:720px) {
    html,body {
        font-size:96px;
    }
}
@media only screen and (max-width: 640px), only screen and (max-device-width:640px) {
    html,body {
        font-size:85.33333333333334px;
    }
}
@media only screen and (max-width: 600px), only screen and (max-device-width:600px) {
    html,body {
        font-size:80px;
    }
}
@media only screen and (max-width: 540px), only screen and (max-device-width:540px) {
    html,body {
        font-size:72px;
    }
}
@media only screen and (max-width: 480px), only screen and (max-device-width:480px) {
    html,body {
        font-size:64px;
    }
}
@media only screen and (max-width: 414px), only screen and (max-device-width:414px) {
    html,body {
        font-size:55.2px;
    }
}
@media only screen and (max-width: 400px), only screen and (max-device-width:400px) {
    html,body {
        font-size:53.333333333333336px;
    }
}
@media only screen and (max-width: 375px), only screen and (max-device-width:375px) {
    html,body {
        font-size:50px;
    }
}
@media only screen and (max-width: 360px), only screen and (max-device-width:360px) {
    html,body {
        font-size:48px;
    }
}
@media only screen and (max-width: 320px), only screen and (max-device-width:320px) {
    html,body {
        font-size:42.66666666666667px;
    }
}
@media only screen and (max-width: 240px), only screen and (max-device-width:240px) {
    html,body {
        font-size:32px;
    }
}
```
