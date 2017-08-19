---
title: HTML中include file的用法
date: 2017-05-28 16:06:43
categories: 'html+css'
tags:
---
在公司上班要求看cp文档须知，发现了一个自己从来没接触听说过的东东。在这里mark一下。以后用到有所体会再补充。
<!-- more -->
## 语法
`<!-- #include PathType = "FileName" --> `
## 参数
### PathType  路径类型
路径可为以下某种类型：
| 文件          | 虚拟          |
| ------------- |:-------------:|
| 该文件名是带有 #include 命令的文档所在目录的相对路径。<br>被包含文件可位于相同目录或子目录中；但它不能处于带有 #include 命令的页的上层目录中。   | 文件名为 Web 站点上虚拟目录的完整虚拟路径。 |

### FileName  指定要包含的文件名
FileName 必须包含文件名扩展，而且必须将文件名用引号 (") 引起来。

注意：

包含 #include 命令的文件必须使用映射到` SSI（Server Side Include）`解释器的文件扩展名；否则，Web 服务器将不处理该命令。默认情况下，扩展名 `.stm、.shtm 和 .shtml `将映射到解释器 (`Ssinc.dll`)。如果安装了 Internet 服务管理器，则可以修改默认扩展映射并添加新的映射。请参阅设置应用程序映射。被包含的文件可具有任何文件扩展名，但建议赋予它们` .inc `扩展名。（在腾讯的cp文档就表明要用`.inc`.

## 示例
`<!--被包含文件与父文件存在于相同目录中。 -->`
`<!-- #include file = "myfile.inc" -->`
`<!--被包含文件位于脚本虚拟目录中。 -->`
`<!-- #include virtual = "/scripts/tools/global.inc" -->`

## include file 与include virtual的区别
1、#include file 包含文件的相对路径，#include virtual包含文件的虚拟路径。

2、在同一个虚拟目录内，`<!--#include file="file.asp"-->`和`<!--#include virtual="file.asp"-->`效果是相同的，但假设虚拟目录名为myweb，则`<!--#include virtual="myweb/file.asp"-->`也可以通过调试，但我们知道`<!--#include file="myweb/file.asp"-->`是绝对要报错的。

3、如果一个站点下有2个虚拟目录myweb1和myweb2，myweb1下有文件file1.asp，myweb2下有文件file2.asp,，如果file1.asp要调用file2.asp，那么在file1.asp中要这样写：`<!--#include virtual="myweb2/file2.asp"-->`，在这种情况下用#include file是无法实现的，用`<!--#include file="myweb2/file2.asp"-->`必然报错。相反，在myweb2的文件中包含myweb1中的文件也是一样。如果该被包含文件在某个文件夹下面，只要在虚拟路径中加上该文件夹即可。

4、不论用#include file 还是 #include virtual，在路径中用“/”还是“\”或者二者交叉使用都不会影响编译效果，程序会顺利执行。

5、以上情况不适用于2个站点文件的相互调用，而且在同一个站点内，`<!--#include file="file.asp"-->`和`<!--#include virtual="file.asp"-->`等效，但假设站点名为website，使用`<!--#include virtual="website/file.asp"-->`是错误的。

-----
转载：http://blog.sina.com.cn/s/blog_962c1f1401011nct.html

> the best for best
