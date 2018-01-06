---
title: 谈一谈Web缓存
date: 2017-09-21 14:43:03
categories: 'js'
---
之前一直觉得web缓存是离我很远的话题，已经看到此类标题的文章往往就是进去默默点个收藏，于是收藏了好多文章。今天沉下心来，认真地开始学习，其实也没想象中难呀~但是里面的概念需要多看多记，所以今天先过一遍，明天再总结。
<!--more-->
## 缓存的分类
web缓存氛围很多种，比如数据库缓存、代理服务器缓存、还有我们熟悉的CDN缓存，以及浏览器缓存。

浏览器先向代理服务器发起Web请求，再将请求转发到源服务器。它属于共享缓存，所以很多地方都可以使用其缓存资源，因此对于节省流量有很大作用。


浏览器缓存是将文件保存在客户端，在同一个会话过程中会检查缓存的副本是否足够新，在后退网页时，访问过的资源可以从浏览器缓存中拿出使用。通过减少服务器处理请求的数量，用户将获得更快的体验

### 强制缓存和协商（对比）缓存
强制缓存即有缓存干脆连请求都不发送了（URL回车）
协商缓存即即使有缓存还是跑去服务器问一下缓存是否修改了（F5刷新）
ctrl + F5  清除缓存跑去拿新数据缓存

## 浏览器缓存
浏览器对于请求资源，拥有一系列成熟的缓存策略。按照发生的时间顺序分别为存储策略、过期策略，协商策略。其中存储策略在收到响应后应用，过期策略，协商策略在发送请求前应用。

简单点来说，发送请求 => 是否有缓存 => （否则请求并触发存储策略） => 过期策略[是否过期] => 协商策略[找服务器验证是否内容过期] => 没过期则响应304，并存储策略[更新缓存时间]  

这里由于导入图片实在太麻烦，而且怕搞到卡死~所以大家可以拿起笔一起画这个逻辑图。

页面的缓存状态是由`header决定的`key,先看表再具体说

| Key | 描述 | 存储策略 | 过期策略 | 协商策略 |
| ------------- | ------------- | -- | -- | -- |
| Cache-Control | 指定缓存机制，覆盖其他设置 | √ | √ | |
| Pragma | http1.0字段，指定缓存机制 | √ |  | |
| Expires | http1.0字段，指定缓存的过期时间 |  | √ | |
| Last-Modified | 资源最后一次的修改时间 |  |  |√ |
| ETag | 唯一标示请求资源的字符串 |  |  |√ |

再看看协商策略如何验证缓存资源

| key | 描述     |
| ------------- | ------------- |
| If-Modified-Since| 缓存校验字段，值为资源最后一次的修改时间，即上次收到的Last-Modified |
| If-Unmodified-Since| 同上，处理方式相反 |
| If-Match | 缓存校验字段，值为唯一标识请求资源的字符串，也就是ETag |
| If-None-Match | 同上，处理方式相反 |

### Cache-Control:
浏览器缓存里, Cache-Control是金字塔顶尖的规则, 它藐视一切其他设置, 只要其他设置与其抵触, 一律覆盖之.

不仅如此, 它还是一个复合规则, 包含多种值, 横跨 存储策略, 过期策略 两种, 同时在请求头和响应头都可设置.

语法为:` “Cache-Control : cache-directive”.`
同样是看表格，其中请求指令7种，响应指令9种。

| Cache-Control | 描述 | 存储策略 | 过期策略 | 请求指令 | 响应指令 |
| ------------- | :-------------| --|--|--|--|
| public  | 资源将被客户端和代理服务器缓存 | √ | | |√ |
| private | 资源仅被客户端缓存 | √ | | |√ |
| no-store | 请求和响应都不缓存 | √ | | √ |√ |
| no-cache | 相当于`max-age:0;must-revalidate`即资源被缓存，但是立即过期，同时下次访问强制验证资源有效性 | √ | √ | √ |√ |
| max-age | 缓存资源，但是在指定时间单位为秒后缓存过期 | √ | √ | √ |√ |
| s-maxage | 同上，依赖public设置，覆盖max-age,且只在代理服务器上有效。 | √ | √ |  |√ |
| max-stale | 指定时间内，即使缓存过时，资源依然有效 |  | √ | √ | |
| min-fresh | 缓存资源至少保持指定时间的新鲜期 |  | √ | √ | |
| must-revalidation/<br/>proxy-revaldation | 如果缓存失败，强制重新向服务器（或代理服务器）发起验证（因为max-state等字段可能改变缓存的失效时间） |  | √ |  | √ |
| only-if-cached | 仅仅返回已经缓存的资源，不访问网络，若无缓存则返回504 |  |  | √ | |
| only-transform | 强制要求代理服务器不要对资源进行转换，禁止代理服务器对`Content-Encoding/Content-Range/Content-Type`字段的修改。因此代理的gulp压缩不被允许 |  |  | √ | √|

假设所请求资源于4月5日缓存, 且在4月12日过期.

当max-age 与 max-stale 和 min-fresh 同时使用时, 它们的设置相互之间独立生效, 最为保守的缓存策略总是有效. 这意味着, 如果max-age=10 days, max-stale=2 days, min-fresh=3 days, 那么:

根据max-age的设置, 覆盖原缓存周期, 缓存资源将在4月15日失效(5+10=15);

根据max-stale的设置, 缓存过期后两天依然有效, 此时响应将返回110(Response is stale)状态码, 缓存资源将在4月14日失效(12+2=14);

根据min-fresh的设置, 至少要留有3天的新鲜期, 缓存资源将在4月9日失效(12-3=9);

由于客户端总是采用最保守的缓存策略, 因此, 4月9日后, 对于该资源的请求将重新向服务器发起验证.

### Pragma
http1.0字段, 通常设置为Pragma:no-cache, 作用同Cache-Control:no-cache. 当一个no-cache请求发送给一个不遵循HTTP/1.1的服务器时, 客户端应该包含pragma指令. 为此, 勾选☑️ 上disable cache时, 浏览器自动带上了pragma字段

### Expires
缓存过期时间，用来指定资源到期的时间，是服务器端的具体的时间点。也就是说，Expires=max-age + 请求时间，需要和Last-modified结合使用。但在上面我们提到过，cache-control的优先级更高。

如果Expires, Cache-Control: max-age, 或 Cache-Control:s-maxage 都没有在响应头中出现, 并且也没有其它缓存的设置, 那么浏览器默认会采用一个启发式的算法, 通常会取响应头的`Date_value` - `Last-Modified_value`值的10%作为缓存时间.

### Last-Modified
语法: Last-Modified: 星期,日期 月份 年份 时:分:秒 GMT
用于标记请求资源的最后一次修改时间, 格式为GMT(格林尼治标准时间). 如可用 new Date().toGMTString()获取当前GMT时间. Last-Modified 是 ETag 的fallback机制, 优先级比 ETag 低, 且只能精确到秒, 因此不太适合短时间内频繁改动的资源. 不仅如此, 服务器端的静态资源, 通常需要编译打包, 可能出现资源内容没有改变, 而Last-Modified却改变的情况.

需要和cache-control共同使用，是检查服务器端资源是否更新的一种方式。当浏览器再次进行请求时，会向服务器传送If-Modified-Since报头，询问Last-Modified时间点之后资源是否被修改过。如果没有修改，则返回码为304，使用缓存；如果修改过，则再次去服务器请求资源，返回码和首次请求相同为200，资源为服务器最新资源。

### ETag
```
ETag:"fcb82312d92970bdf0d18a4eca08ebc7efede4fe"
```
实体标签, 服务器资源的唯一标识符, 浏览器可以根据ETag值缓存数据, 节省带宽. 如果资源已经改变, etag可以帮助防止同步更新资源的相互覆盖. ETag 优先级比 Last-Modified 高.

使用ETag可以解决Last-modified存在的一些问题：
  - 某些服务器不能精确得到资源的最后修改时间，这样就无法通过最后修改时间判断资源是否更新
  - 如果资源修改非常频繁，在秒以下的时间内进行修改，而Last-modified只能精确到秒
  - 一些资源的最后修改时间改变了，但是内容没改变，使用ETag就认为资源还是没有修改的。

### If-Mathch
语法: `If-Match: ETag_value` 或者 `If-Match: ETag_value, ETag_value, …`

缓存校验字段, 其值为上次收到的一个或多个etag 值. 常用于判断条件是否满足, 如下两种场景:
- 对于 GET 或 HEAD 请求, 结合 Range 头字段, 它可以保证新范围的请求和前一个来自相同的源, 如果不匹配, 服务器将返回一个416(Range Not Satisfiable)状态码的响应.
- 对于 PUT 或者其他不安全的请求, If-Match 可用于阻止错误的更新操作, 如果不匹配, 服务器将返回一个412(Precondition Failed)状态码的响应.

### If-None-Match
语法: `If-None-Match: ETag_value 或者 If-None-Match: ETag_value, ETag_value, …`

缓存校验字段, 结合ETag字段, 常用于判断缓存资源是否有效, 优先级比If-Modified-Since高.

对于 GET 或 HEAD 请求, 如果其etags列表均不匹配, 服务器将返回200状态码的响应, 反之, 将返回304(Not Modified)状态码的响应. 无论是200还是304响应, 都至少返回 Cache-Control, Content-Location, Date, ETag, Expires, and Vary 中之一的字段.

对于其他更新服务器资源的请求, 如果其etags列表匹配, 服务器将执行更新, 反之, 将返回412(Precondition Failed)状态码的响应.

### If-Modified-Since
缓存校验字段, 其值为上次响应头的Last-Modified值, 若与请求资源当前的Last-Modified值相同, 那么将返回304状态码的响应, 反之, 将返回200状态码响应.

### If-Unmodified-Since
缓存校验字段, 语法同上. 表示资源未修改则正常执行更新, 否则返回412(Precondition Failed)状态码的响应. 常用于如下两种场景:
- 不安全的请求, 比如说使用post请求更新wiki文档, 文档未修改时才执行更新.
- 与 If-Range 字段同时使用时, 可以用来保证新的片段请求来自一个未修改的文档.

## 怎么让浏览器不缓存静态资源
实际上, 工作中很多场景都需要避免浏览器缓存, 除了浏览器隐私模式, 请求时想要禁用缓存, 还可以设置请求头: Cache-Control: no-cache, no-store, must-revalidate .

当然, 还有一种常用做法: 即给请求的资源增加一个版本号, 如下:
```
<link rel="stylesheet" type="text/css" href="../css/style.css?version=1.8.9"/>
```
这样做的好处就是你可以自由控制什么时候加载最新的资源.

不仅如此, HTML也可以禁用缓存, 即在页面的\节点中加入\标签, 代码如下:
```
<meta http-equiv="Cache-Control" content="no-cache, no-store, must-revalidate"/>
```
上述虽能禁用缓存, 但只有部分浏览器支持, 而且由于代理不解析HTML文档, 故代理服务器也不支持这种方式.

## H5 Application
hmm 以后看看写专门一篇来说吧
http://www.cnblogs.com/etoah/p/5579622.html

----
参考文章：
[浅谈 Web 缓存](https://mp.weixin.qq.com/s/MLmxeIlX6Zy7Uy98SEWbFw)
[【第903期】浏览器缓存机制剖析](https://mp.weixin.qq.com/s/yf0pWRFM7v9Ru3D9_JhGPQ)
[【第489期】透过浏览器看HTTP缓存](https://mp.weixin.qq.com/s?__biz=MjM5MTA1MjAxMQ==&mid=401841369&idx=1&sn=8d1631bc856a3b290bc5468c20a4ccab&scene=21)
[HTTP 缓存机制详解](https://mp.weixin.qq.com/s/8UXEMQBkV9hHwtu9R7mV5w)
[【第975期】掌握 HTTP 缓存——从请求到响应过程的一切（下）](https://mp.weixin.qq.com/s/0ZgM2jW2a0OUziBMYVnsOg)
[web性能优化:详说浏览器缓存](http://www.cnblogs.com/etoah/p/5579622.html)
