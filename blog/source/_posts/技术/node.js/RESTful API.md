---
title: RESTful API
date: 2017-08-17 18:55:10
categories: 'node.js'
tags:
---
我这个后台接口都是按照`RESTful架构规范`来的，你这种要求不符合规范，不能这么做。
<!-- more -->
在公司后不久，我就对后台小哥这句话几乎耳熟能详了。然后，我就觉得，天呐，后台小哥你真棒！！千错万错都是我的错，是我提出无礼要求的，是我失礼了！话不多说，带入正题，今天认真来捣鼓一波`RESTful架构`是个什么什么玩意~

## 起源
随着WebApp的兴起，**网站即软件**的概念深入“人心”。但是网站和软件在以前却是不同的领域，于是我们开始要考虑如何融合着两者。**如何开发在互联网环境中使用的软件。**

于是各种规范架构就诞生了，而`RESTful`架构就是其中一种，被广大网站采用。提出者，就是大名鼎鼎的Fielding博士（虽然其实我也不太认识2333）

## 名称
为了理解这个东东，我们必须对它的命名有足够的认识。`REST`即Representational State Transfer,翻译成人话就是“表现层状态转化”，再扩展一下谁的表现层=>**资源表现层状态转化**。很好，我们已经买成了成功一步！扩展完，我们再拆分。（就想议论文一样总分总）

### 资源（Resources）
**资源，就是网络上的一个实体，或者说一个具体的信息。**它可以是一段文本，一张图片，一个网站，一首音乐，一个种子，一段隐秘的小视频。总之，它就是网络上具体实在的一个东东。现在我们可以用一个URI(统一资源定位符)指向它，每种资源对应一个特定的URI。要获取这个资源，访问它的URI即可。栗子：你要访问腾讯爸爸的官网，输入`www.qq.com`这个URL 就可以拿到爸爸给你他家的首页，里面有文本啦，图片啦，还有小视频等等资源（内部封装了更多的URI进行获取那些资源）
> URI是统一资源标识符，而URL是统一资源定位符。笼统地说，每个URL都是URI，但是反之则不成立。

> 一个 URI 实例和一个支持语法意义上的、依赖于方案的比较、规范化、解析和相对化计算的结构化字符串差不多。

> URL 是一个结构化字符串，它支持解析的语法运算以及查找主机和打开到指定资源的连接之类的网络 I/O 操作。

### 表现层(Representation)
“资源”是一种信息的实体，但是它却可以有多重外在表现形式。**我们把“资源”呈现出来的形式，叫做它的“表现层”。**

例如，文本，可以用txt格式存储，可以用word存储，可以用JSON/XML/MD等等格式进行储存。图片则有JPG/PNG/GIF/BASE64等等形式存储。

URI只代表着资源的实体，不包含形式。也就是说，你输入`http://www.cnblogs.com/artech/p/3506553.html`的时候去定位到的是`3506553.html`，但是后缀名不是必要的，你可以把`.html`去掉，因为它表示的是格式，属于“表现层”，而URI应该只代表“资源”的位置。（这段有点绕，因为没有实际的URI可以进行使用，所有略微难以解释）

然后你就会发现，丫的去掉`.html`根本就不行，访问不了。那是因为你需要再HTTP请求的头信息去定义`Accept`和`Content-Type`字段来描述“表现层”。——表现层的规范写法。

### 状态转化（State Transfer）
深吸一口气，快搞定了！！努力一把，就学会这个知识点了哦！

访问一个网站，就代表了客户端和服务器的一个互动过程。这个过程一定会有数据的提交和状态的变化（例如是否登录状态）。然而，你经常都会听到的一句**HTTP协议，是一个无状态的协议。**这意味着，所有的状态都保存在服务器端。因此，如果客户端想要操作服务器，必须通过某种手段，让服务器端发生“状态转化”。而这种转化是建立在表现层之上的，所以这就是“表现层状态转化”。（因为状态的改变是资源的变化嘛）

而客户端通过的某种手段，其实就只有HTTP协议。协议里面五个操作词。
- `GET` 用来获取资源
- `POST` 用来新建资源
- `UPDATE` 用来更新资源的部分信息
- `PUT` 用来更新资源的全部信息
- `DELETE` 用来删除资源

## 综上
什么是`RESTful`架构：
  1. 每一个URI代表一种资源
  2. 客户端和服务器之间，传递这种资源的某种表现层；
  3. 客户端通过HTTP协议定义的动作进行服务器资源操作，实现表现层状态转化。

好了，`RESTful`架构学习完了。这时候，如果我这么说你肯定就打死我了，因为感觉就是学了一遍名词解释啊喂，一点实操都没有，连能干嘛都不知道。没事，我们理解了这个名词，再来点实战题调味，就很舒服了。

## 设计API
### Q1 URI不允许包含动词。
资源是一种实体，所有URI中不应该包含动词，动词应该放在HTTP协议中。举例：
`/posts/show/1`，`show`是动词，嘟嘟，WRONG!!正确的写法是`/posts/1`，然后用`GET`方法表示
```
GET /posts/1
```

如果有些动作是HTTP动词表示不了的， 你就应该把动作看做一种资源。比如网上汇款，从账号1汇款500到账号2.
```
POST /accounts/1/transfer/500.to/2
```
嘟嘟嘟嘟！！ALL WRONG!!正确的写法应该是把动词transfer改成名词transaction,资源不能是动词，但是可以是一种服务。
```
POST /transaction HTTP/1.1
Host: 127.0.0.1

from:1&to=2&amount=500.00
```
### Q2 API总是使用HTTPs协议，并部署在专用域名下。
这也就是为什么小程序要求HTTPs协议使用，因为安全性规范性吧 我猜。
```
https://apis.example.com // 或者
https://example.org/api/
```
涉及版本号的问题的话，这里又分歧。规范中建议将版本号放在HTTP的头信息中
```
Accept: vnd.example-com.foo+json; version=1.0
```
然而，实际上不如放入URL来得方便直观
```
https://api.example.com/v1/
```
### Q3 如何设计URI
每个网址代表一个资源，所以地址中不能有动词，只有名词。名词往往和数据库的表格名保持一致。一般来说，数据库中的表都是同种记录的集合，所以API中的名词应该使用复数，举例,有一个API提供动物园信息，包括动物雇员什么的
```
https://api.example.com/v1/zoos
https://api.example.com/v1/animals
https://api.example.com/v1/employees
```
### Q4 HTTP动词
在上面的五个操作次已经提到了。直接给栗子吧
- `GET /zoos`：列出所有动物园
- `POST /zoos`：新建一个动物园
- `GET /zoos/ID`：获取某个指定动物园的信息
- `PUT /zoos/ID`：更新某个指定动物园的信息（提供该动物园的全部信息）
- `PATCH /zoos/ID`：更新某个指定动物园的信息（提供该动物园的部分信息）
- `DELETE /zoos/ID`：删除某个动物园
- `GET /zoos/ID/animals`：列出某个指定动物园的所有动物
- `DELETE /zoos/ID/animals/ID`：删除某个指定动物园的指定动物

### Q5 设计过滤信息参数
- `?limit=10`：指定返回记录的数量
- `?offset=10`：指定返回记录的开始位置。
- `?page=2&per_page=100`：指定第几页，以及每页的记录数。
- `?sortby=name&order=asc`：指定返回结果按照哪个属性排序，以及排序顺序。
- `?animal_type_id=1`：指定筛选条件

参数的设计允许存在冗余，即允许API路径和URL参数偶尔有重复。比如，GET /zoo/ID/animals 与 GET /animals?zoo_id=ID 的含义是相同的。

### Q6 状态码的返回
服务器向用户返回的状态码和提示信息，常见的有以下一些（方括号中是该状态码对应的HTTP动词）。

- 200 OK - [GET]：服务器成功返回用户请求的数据，该操作是幂等的（Idempotent）。
- 201 CREATED - [POST/PUT/PATCH]：用户新建或修改数据成功。
- 202 Accepted - [*]：表示一个请求已经进入后台排队（异步任务）
- 204 NO CONTENT - [DELETE]：用户删除数据成功。
- 400 INVALID REQUEST - [POST/PUT/PATCH]：用户发出的请求有错误，服务器没有进行新建或修改数据的操作，该操作是幂等的。
- 401 Unauthorized - [*]：表示用户没有权限（令牌、用户名、密码错误）。
- 403 Forbidden - [*] 表示用户得到授权（与401错误相对），但是访问是被禁止的。
- 404 NOT FOUND - [*]：用户发出的请求针对的是不存在的记录，服务器没有进行操作，该操作是幂等的。
- 406 Not Acceptable - [GET]：用户请求的格式不可得（比如用户请求JSON格式，但是只有XML格式）。
- 410 Gone -[GET]：用户请求的资源被永久删除，且不会再得到的。
- 422 Unprocesable entity - [POST/PUT/PATCH] 当创建一个对象时，发生一个验证错误。
- 500 INTERNAL SERVER ERROR - [*]：服务器发生错误，用户将无法判断发出的请求是否成功。

### Q7 错误处理
一般都返回`error`键名表示
```
{
  error: 'invalid api key'
}
```

### Q8 如何返回结果
- GET /collection：返回资源对象的列表（数组）
- GET /collection/resource：返回单个资源对象
- POST /collection：返回新生成的资源对象
- PUT /collection/resource：返回完整的资源对象
- PATCH /collection/resource：返回完整的资源对象
- DELETE /collection/resource：返回一个空文档

### Q9 Hypermedia API
这一段其实我也不太能理解。大致的意思就是借口可以返回这个API与当前网址的关系，`href`表示API的路径，`title`表示API的标题，`type`表示返回的类型。方便用户使用，可以避免查询文档。

比如，当用户向api.example.com的根目录发出请求，会得到这样一个文档。
```
{"link": {
  "rel":   "collection https://www.example.com/zoos",
  "href":  "https://api.example.com/zoos",
  "title": "List of zoos",
  "type":  "application/vnd.yourformat+json"
}}
```

Github的API就是这种设计，访问api.github.com会得到一个所有可用API的网址列表。
```
{
  "current_user_url": "https://api.github.com/user",
  "authorizations_url": "https://api.github.com/authorizations",
  // ...
}
```
从上面可以看到，如果想获取当前用户的信息，应该去访问api.github.com/user，然后就得到了下面结果。
```
{
  "message": "Requires authentication",
  "documentation_url": "https://developer.github.com/v3"
}
```

### Q10 服务器返回的数据格式
现在一般都是用JSON格式返回。

### Q11 API接口的无状态性
RESTful只要维护资源的状态，而不是维护客户端的状态。对于它来说，每次请求都是全新的，它只需要针对本次请求作相应的操作，不需要讲本次请求的相关信息记录下来以便于后续来自相同哭护短请求的处理。**简单地说，就是客户端维护自己的状态，由客户端来告诉服务器我当前状态是什么**栗子：翻页操作。

------------------------------
参考文章：
[我所理解的RESTful Web API [设计篇]](http://www.cnblogs.com/artech/p/3506553.html)
[RESTful API 设计指南](http://www.ruanyifeng.com/blog/2014/05/restful_api.html)
[理解RESTful架构](http://www.ruanyifeng.com/blog/2011/09/restful.html)
