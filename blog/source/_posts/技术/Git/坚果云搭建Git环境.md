---
title: 坚果云搭建Git环境
date: 2017-05-25 15:39:15
categories: 工具配置
tags:
---
[你的github-通过坚果云管理您的代码](http://blog.jianguoyun.com/?p=321)
这是官方维护的博客，就不担心他哪一天消失啦~~所以直接贴地址就行。

下面教教如何使用：
<!-- more -->
## Git的使用
其实都是些简单的命令，稍微学几个常用的就行。高级的估计也暂时用不到。所以自行百度吧。

## 假设两个人（A、B）要远程协作：
### A同学
坚果云共享目录为：`/Users/A/NutsCloud`（注释：假设两位同学，已经都同步了该文件夹；如果没有，那创建者邀请一下即可）

本地工作目录为：`/Users/A/KidsJoy`


#### 步骤一：建立本地仓库
```
~/KidsJoy $> git init        
(注释：初始化git repository)
~/KidsJoy $> git add .       
 (注释：空目录执行该命令可能会报错)
~/KidsJoy $> git commit -m "first commit"  
(注释：至此，本地仓库建立，并完成第一次提交）
```
#### 步骤二：建立同步文件夹，并建立裸仓库
该仓库下只生成记录版本库历史记录的.git目录下的文件，而不包含.git目录、实际项目源文件的拷贝，所以该版本库不能成为工作目录working tree，及推送本地仓库到远端
```
~/KidsJoy $> mkdir -p ~/NutsCloud/KidsJoy.git        
(注释：-p选项，表示可以同时创建多层目录)
~/KidsJoy $> cd ~/NutsCloud/KidsJoy.git
~/KidsJoy.git $> git init --bare
~/KidsJoy $> cd ~/KidsJoy
~/KidsJoy $> git remote add orig ~/NutsCloud/KidsJoy.git       
 (注释：添加一个标记，让orig指向~/NutsCloud/KidsJoy.git，操作orig的时候等同于操作××)
~/KidsJoy $> git push orig master        
(注释：将本地仓库提交到远程仓库的master分支）
  ```

### B同学
坚果云共享目录为：`/Users/B/NutsCloud`        （注释：此时该目录下应该同步到，`KidsJoy.git`文件夹）

本地工作目录为：`/Users/B/KidsJoy`
#### 建立本地仓库、拉取并提交
```
~/KidsJoy $> git init
~/KidsJoy $> git pull /Users/B/NutsCloud/KidsJoy.git master
~/KidsJoy $> vi test.txt        (注释：此处省略其他操作，就是新建一个txt文件）
~/KidsJoy $> git add .
~/KidsJoy $> git commit -m "second commit"        (注释：提交到本地仓库。这步可能报错，需要你写上email，name）
~/KidsJoy $> git push /Users/B/NutsCloud/KidsJoy.git master        (注释：推送到远程）
```
至此，协作流程的大概就是这样了。
