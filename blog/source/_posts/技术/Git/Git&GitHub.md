---
title: Git&GitHub Notes
date: 2017-2-29 10:32:44
categories: "Git"
tags:
     - Git
---
使用git时候记录下来的命令指南
<!-- more -->

## git 配置
1. git config –global color.ui auto ##获得彩色输出
2. 启动编辑器命令：
  1. git config --global core.editor "'C://..sublime_text.exe' -n -w"
  2. git config --global push.default upstream
  3. git config --global merge.conflictstyle diff3
  4. alias sub1=”url”
  3. git config –global core.autocrlf true ##将autocrlf全局属性设置
4. git config --system core.longpaths true。

---
## git init初始化版本库

---
## git diff 比较版本区别
1. git diff SHA1 SHA2 ##比较两个版本的差异
2. git diff  ##查看尚未暂存的文件更新了哪些部分（working tree ＆ index）
3. git diff HEAD ##显示working tree 和 HEAD版本的区别
4. git diff –staged ##显示index 和HEAD 的区别
5. git diff topic…master##显示topic 和 master 分别开发以来，master分支上的变化
6. git diff –stat ##显示简单的diff结果
7. git diff HEAD^ HEAD ##显示上次commit和上上次commit
8. git diff –u ##可以在diff结果中显示上下文信息。

---
## git commit 提交版本
1. git commit –m “message” ##提交版本
2. git commit –a –m “message”  ## -a选项可只将所有被修改或者已删除的且已经被git管理的文档提交倒仓库中。
3. git commit –amend ##对于已经修改提交过的注释进行修改

---
## git add 与 git reset
1. git add除了能够判断出当前目录（包括其子目录）所有被修改或者已删除的文档，还能判断用户所添加的新文档，并将其信息追加到索引中。
2. git reset 可以删除不想添加到暂存区的文件

---
## git log 查看提交记录

---
## git clone 克隆

---
## git checkout 检出代码的旧版本
1. git checkout ‘ID’
2. git checkout master ##离开“分离的HEAD”状态

---
## git branch 创建分支

---
## git merge 合并操作
1. git merge branch1 branch2 ##将branch2合并到branch1中去
2. git merge –abort ##将文件恢复到你开始合并之前的状态

---
## git show 将提交与所在分支进行对

---
## git status查看被修改的文件

---
## git remote 远程操纵库
1. git remote 不带参数，列出已经存在的远程分支
2. git remote -v | --verbose 列出详细信息，在每一个名字后面列出其远程url
3. git remote add [name] url	##添加一个远程仓库

---
## git fetch 将远程代码库的最新提交复制到本地
1. git fetch origin ##更新本地副本
2. git pull 等价于 git fetch + git merge.

---
## git pull git push 拉取与推送
1. git push origin (master/other branch)

---
## git reset --hard 强制回滚到版本
1. 配合`git push --force`可以强制删除已经commit的内容。但是期间提交的东西都会消失。

## git stash 和 git stash pop 不commit的情况下pull
git stash 可用来暂存当前正在进行的工作， 比如想pull 最新代码， 又不想加新commit， 或者另外一种情况，为了fix 一个紧急的bug,  先stash, 使返回到自己上一个commit, 改完bug之后再stash pop, 继续原来的工作。
基础命令：
`$git stash`
`$do some work`
`$git stash pop`
`git stash save "work in progress for foo feature"`
当你多次使用’git stash’命令后，你的栈里将充满了未提交的代码，这时候你会对将哪个版本应用回来有些困惑，

`’git stash list’ `命令可以将当前的Git栈信息打印出来，你只需要将找到对应的版本号，例如使用`’git stash apply stash@{1}’`就可以将你指定版本号为stash@{1}的工作取出来，当你将所有的栈都应用回来的时候，可以使用’`git stash clear’`来将栈清空。
```
git stash          # save uncommitted changes
# pull, edit, etc.
git stash list     # list stashed changes in this git
git show stash@{0} # see the last stash
git stash pop      # apply last stash and remove it from the list

git stash --help   # for more info
```

## 在新电脑使用git
在新电脑上使用git

设置Git的 name和email
```
git config --global user.name "singlemai"
git config --global user.email "422107224@qq.com"
```
生成SSH密钥
```
查看是否已经有了ssh密钥: cd ~/.ssh
如果没有密钥则不会有此文件夹，有则备份删除
生成密钥：
ssh-keygen -t rsa -C "422107224@qq.com"
按3个回车，密码为空。
最后得到了两个文件： id_rsa和 id_rsa.pub
```
添加密钥到ssh: ssh-add 文件名，需要之前输入密码。

在github上添加ssh密钥，这要添加的是 id_rsa.pub 里面的公钥
打开github设置，添加新的公钥就可以啦~~
