---
title: 在windows下用nvm管理node
date: 2017-10-15 18:34:10
categories: 工具配置
tags:
---
哭唧唧，配置了老半天，这坑踩得也太没必要了点。anyway,还是稍微有长见识，mark一下吧
<!-- more -->
重要事情说三遍！引入新东西先查教程先查源文档！引入新东西先查教程先查源文档！引入新东西先查教程先查源文档！

其实讲道理，我这个做法是应该直接一波流配完直接搞定的。奈何不知道是不是版本问题。。最新版出了点bug，捣鼓了老久找了个以前的版本才终于过了关。

## 安装前提
咱们直接先看文档的[]安装提示](https://github.com/coreybutler/nvm-windows)：
> It comes with an installer (and uninstaller), because getting it should be easy. Please note, you need to uninstall any existing versions of node.js before installing NVM for Windows. Also delete any existing nodejs installation directories (e.g., "C:\Program Files\nodejs") that might remain. NVM's generated symlink will not overwrite an existing (even empty) installation directory.
You should also delete the existing npm install location (e.g. "C:\Users<user>\AppData\Roaming\npm") so that the nvm install location will be correctly used instead. After install, reinstalling global utilities (e.g. gulp) will have to be done for each installed version of node:

全英文？很可怕？那也得看啊，翻译着看呗，不然踩坑踩死你。这里大致的意思就是如果你以前安装过node.js，那么在安装nvm之前必须把它卸载掉。然后它大概根据默认配置让你去找你安装位置。之后，还需要删除掉用npm下载的所有包。反正就是删删删，全删干净。确保之后不会有冲突

## 开始
然后就是直接找最近的版本下载，奈何最近墙太厚翻不过去（其实主要也是自己很少去翻）。所以下载的是源代码然后build跑出来的。不知道是版本没弄好还是怎样，缺了一个文件`nvm.exe`。所以一直没能成功安装上。之后...就是很烦人地找旧版本了，还是因为墙的原因，下载简直难产。感谢前辈居然贴出百度云链接。http://pan.baidu.com/s/1qXKQrnQ. 然后就直接安装就好了。

如果你读了文档，你就会发现其实直接所有都是默认安装最好。因为在以后更新的时候，你直接抓一个新的包直接重新默认安装一遍就直接OK了。不过要是对文件目录喜欢规划的，也没问题。（这里有一个小坑，是在查版本更迭的时候发现的，百度云的这个版本没有修复文件名带空格和特殊字符的情况，所以默认安装的时候会导致出错（我电脑上默认的位置文件夹名有空格）于是我更换了自己的安装目录就解决啦。

### 第二步，安装
安装完后，在安装目录下应该有如下文件。
`elevate.cmd、elevate.vbs、install.cmd、LICENSE、nvm.exe、settings.txt`
直接打开`settings.txt`
这里如果你没有写错应该是这样的.root代表的是你nvm的安装目录，nodejs（听说只能取这个文件名）代表的是安装node的位置。
```
root: C:\dev\nvm
path: C:\dev\nodejs
```
为了避免墙阻碍咱们办事，直接给他加上一段镜像.arch代表的是电脑是多少位系统
```
root: C:\dev\nvm
path: C:\dev\nodejs
arch: 64
proxy: none
node_mirror: http://npm.taobao.org/mirrors/node/
npm_mirror: https://npm.taobao.org/mirrors/npm/
```

### 第三步，配置环境变量（这一步已经自动化了，只是方便查看）
环境变量的系统变量中，生成两个环境变量：NVM_HOME 和 NVM_SYMLINK 我们开始修改这两个变量名的变量值：NVM_HOME的变量值为：C:\dev\nvm； NVM_SYMLINK的变量值为：C:\dev\nodejs。在Path中也会自动添加上C:\dev\nvm;或者是C:\dev\nodejs，如果有的话，把他们删掉，没有的话更好，我们自己来配置，在Path的最前面输入： ;%NVM_HOME%;%NVM_SYMLINK%;

然后就可以直接在控制台输入`nvm v`查看有没成功了。

### 第四步，安装最新的nodejs版本
继续输入命令：`nvm install latest` 如果网络畅通，我们会看到正在下载的提示，下载完成后 会让你use那个最新的node版本。如果你是第一次下载，在use之前，`C:\dev`目录下是没有`nodejs`这个文件夹的，在输入比如： `nvm use 5.11.0` 之后，你会发现，C:\dev目录下多了一个nodejs文件夹，这个文件夹不是单纯的文件夹，它是一个快捷方式，指向了 C:\dev\nvm 里的 v5.11.0 文件夹。

同样的咱们可以下载其他版本的nodejs，这样通过命令:`nvm use` 版本号 比如：`nvm use 5.11.0`就可以轻松实现版本切换了。
> 备注： 如果你的电脑系统是32 位的，那么在下载nodejs版本的时候，一定要指明 32 如： nvm install 5.11.0 32 这样在32位的电脑系统中，才可以使用，默认是64位的。

### 第五步，安装npm
为了统一起见，我们安装一个全局的npm工具，这个操作很有必要，因为我们需要安装一些全局的其他包，不会因为切换node版本造成原来下载过的包不可用。(重点来啦)

1. 首先我们进入命令模式，输入 `npm config set prefix "C:\dev\nvm\npm"` 回车，这是在配置npm的全局安装路径，然后在用户文件夹下会生成一个.npmrc的文件，用记事本打开后可以看到如下内容：
```
prefix=C:\dev\nvm\npm
```
2. 然后继续在命令中输入： `npm install npm -g `回车后会发现正在下载npm包，在C:\dev\nvm\npm目录中可以看到下载中的文件，以后我们只要用npm安装包的时候加上 -g 就可以把包安装在我们刚刚配置的全局路径下了。

3. 我们为这个npm配置环境变量： 变量名为：`NPM_HOME`，变量值为 ：`C:\dev\nvm\npm`

4. 在Path的最前面添加`;%NPM_HOME%`，注意了，这个一定要添加在 `%NVM_SYMLINK%`之前，所以我们直接把它放到Path的最前面

5. 最后我们新打开一个命令窗口，输入npm -v ,此时我们使用的就是我们统一下载的npm包了。

6. 同样的我们还可以安装cnpm工具，它是中国版的npm镜像库，地址在这里：https://cnpmjs.org/，也是npm官方的一个拷贝，因为我们和外界有一堵墙隔着，所以用这个国内的比较快，淘宝也弄了一个和npm一样的镜像库，http://npm.taobao.org/，它和官方的npm每隔10分钟同步一次。安装方式：
  - npm install -g cnpm --registry=http://r.cnpmjs.org
  - 或者用淘宝的npm install -g cnpm --registry=https://registry.npm.taoba.org
  - 安装好了cnpm后，直接执行cnpm install 包名比如：cnpm install bower -g 就可以了。-g只是为了把包安装在全局路径下。如果不全局安装，也可以在当前目录中安装，不用-g就可以了。


自此，终于可以用了。

---
感谢前辈
[nodejs在windows下的安装配置(使用NVM的方式)](http://blog.csdn.net/tyro_java/article/details/51232458)
[window下通过nvm-windows来安装多版本node](http://blog.csdn.net/qq_36423639/article/details/70230571)
[在windows下用nvm 安装node](http://www.jianshu.com/p/28bca6529150)
[npm 如何设置镜像站](http://blog.csdn.net/hdchangchang/article/details/46623919)
[nvm-windows](https://github.com/coreybutler/nvm-windows)
