---
title: Atom配置
date: 2017-05-20 15:53:58
categories: 工具配置
tags:
   - atom
---
Atom是一个很好用的编辑软件，可以让自己添加各种插件来辅助自己写代码。然而，有时候配置多了可能就忘了。所以特意建一个来保存自己的基础信息。（虽然说有插件可以实现转移，不过还是不放心吧。)
<!-- more -->
## keymap的配置
实现vue中支持emmet，和快捷键打开浏览器(需要先安装`open-in-browser`)。
```
./keymap.cson

'atom-text-editor':
  'ctrl-F12' : 'open-in-browser:open'
# 让vue文件里面支持emmet语法
'atom-text-editor[data-grammar~="vue"]:not([mini])':
  'tab': 'emmet:expand-abbreviation-with-tab'
```
## 代码高亮显示
代码高亮其实很好找，so 不罗列太多。大致名字都是`atom-${name}`和`language-${name}`

vue代码高亮：
亲测`language-vue`比`language-vue-componment`要好用啊。
jade高亮：
`atom-jade`

## 辅助插件
### 同时修改html开闭合标签`atom-double-tag`
### 高亮选择单词`highlight selected`
emmet
`emmet-atom`
颜料
`atom-pignments`
`color-picker`
缩略图
`minimap`
文件图标
`file-icon`
美化工具插件
`atom-beautify`
这个是美化代码的插件，支持所有主流语言。不过嘛，设置起来有些复杂（主要是英文的原因）然后之前在项目中又可能代码量太大，总是莫名出bug，所以被我闲置了。
`autocomplete-paths`
这个自动引入文件路径提供显示实在太棒！这是我工作之后接触webstorm发现的一个不能太棒的功能。
`autoprefixer`
自动添加css前缀，不错的一个插件，但是考虑到我打算尽可能去使用预编译语言，所以。hmm，只当留个记录吧。然后顺便在这里记录一下我用koala来处理sass文件。
`docblockr`
写注释的插件。hmm，同样是留个记录的东西，因为对我暂时的需求而言，我只需要一个命令。那么其实使用`snippt`来实现其实也可以，所以暂时还是使用`snippt`来实现好了。文章后面会提到

`代码提示 atom-ternjs`
其实很久之前我就下载了这个，但是比较无脑，没认真看官方文档。没留意到他是需要根据每个特定项目进行设置的。也就是要在每个项目里面调出这个插件的设置面板`packages => atom ternjs => configure project ` 记得勾选`browser` 即可享用。不过据说能少用就少用，不然有些代码会忘了=。=

## markdown
集各种功能的markdown编辑插件
`markdown-preview-enhanced`

## 其他小地方的调整
调出控制面板
  - 可以调试自动缩进`auto indent`
  - 自动换行`soft wrap`
  - 显示那条对齐线`show indent guide`

## Snippets的设置
因为只给自己看，所以就不长篇科普了。直接贴：在根目录下`snippets.cson` 。好像不支持`/`这种关键符号，所以注释只能用`annotation`来说明了。具体要看[`cson`](http://flight-manual.atom.io/using-atom/sections/snippets/)的文档
```
'.text.html.vue':
  'Vue construct':
    'prefix': 'vue'
    'body': """
      <template>
        $1
      </template>

      <script type="text/ecmascript-6">
        export default {

        }
      </script>

      <style scoped lang="stylus" rel="stylesheet/stylus">
      </style>
    """

  'annotation':
    'prefix': 'annotation'
    'body': """
    /**
   * [${1:function description}]
   * ${2:This is a cool function}
   * @param  {${3:type}} x ${4:description}
   * @param  {${5:type}} x ${6:description}
   * @param  {${7:type}} x ${8:description}
   * @return {${9:type}}
   */
    """
'.source.js':
  'annotation':
    'prefix': 'annotation'
    'body': """
    /**
   * [${1:function description}]
   * ${2:This is a cool function}
   * @param  {${3:type}} x ${4:description}
   * @param  {${5:type}} x ${6:description}
   * @param  {${7:type}} x ${8:description}
   * @return {${9:type}}
   */
    """
```
