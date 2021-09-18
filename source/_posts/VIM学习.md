---
title: VIM学习
date: 2021-02-24 05:55:20
categories: Server
tags: [Vim, 记录, Linux]
description: 对于VIM的学习和配置
keywords: 
cover: false
---
# VIM学习

## 一、学习资源

- 官方教程 `vimtutor`
- 网页交互式游戏：
  - [Interactive Vim tutorial](https://www.openvim.com/tutorial.html)
  - [Vim-adventures](https://vim-adventures.com/)
- Github：
  - [Learn-Vim (English)](https://github.com/iggredible/Learn-Vim) [Learn-Vim_zh_cn (Chinese)](https://github.com/wsdjeg/Learn-Vim_zh_cn)
  - [vim-galore (English)](https://github.com/mhinz/vim-galore) [vim-galore-zh_cn (Chinese)](https://github.com/wsdjeg/vim-galore-zh_cn#%E4%BB%80%E4%B9%88%E6%98%AF-vim)
- 文档：自带英文文档，也可以改成[中文文档](https://github.com/yianwillis/vimcdoc)
- 书籍：
  - [笨办法学Vimscript](https://www.kancloud.cn/kancloud/learn-vimscript-the-hard-way/49321)
  - VIM实用技巧
- 其他：
  - [VIM 教程网](https://vimjc.com/archives/)
  - [VIM 命令表单 Vim Cheat Sheet](https://vim.rtorr.com/lang/zh_cn)

**如何使用资源**

&emsp;&emsp;`vimtutor`和两个网页交互式游戏主要是教一些简单的操作，方便vim的上手；Github上的两个项目是相对系统地来讲述vim的，个人倾向于Learn Vim，vim-galore与其互相补充。

&emsp;&emsp;把前面说的这些都差不多过完后，应该已经能满足大多数常用操作并且对vim有个大概的认识了，接下来可以以实践为主，遇到什么问题就去Google，有能力的话就直接看文档。

## 二、插件配置

### 2.1 插件管理器 [Vim-plug](https://github.com/junegunn/vim-plug)

### 2.2 主题

比较中意这几个，选择的都是暗色系

- [Dracula](https://draculatheme.com/)
- [Gruvbox](https://github.com/morhetz/gruvbox)
- [Solarized](https://github.com/altercation/vim-colors-solarized)
- [Molokai](https://github.com/tomasr/molokai)

另外状态栏美化使用插件[vim-airline](https://github.com/vim-airline/vim-airline)，主题使用配套主题[vim-airline-themes](https://github.com/vim-airline/vim-airline-themes)

### 2.3 语言服务器客户端 [Coc.nvim](https://github.com/neoclide/coc.nvim)

&emsp;&emsp;依赖于`node.js`，支持插件，可以配置语言服务器。直接按readme里给的[Example vim configuration](https://github.com/neoclide/coc.nvim#example-vim-configuration)配置即可，根据个人喜好更改。

&emsp;&emsp;要注意如果配置了`<tab>`作为补全按键的话，会与Ultisnips的`<tab>`按键冲突，可以改按键映射，也可以使用[coc-snippets](https://github.com/neoclide/coc-snippets)替代Ultisnips。

&emsp;&emsp;关于语言服务器协议可以参见[LSP](https://microsoft.github.io/language-server-protocol/)


### 2.4 C系语言服务器 [ccls](https://github.com/MaskRay/ccls)

&emsp;&emsp;这个插件的安装配置过程挺麻烦，使用前仔细查看[官方wiki](https://github.com/MaskRay/ccls/wiki)。在Windows上build的时候尽量不要使用msys2，可能会出现msys2终端里可以使用，但是Win命令行无法使用ccls的情况，参见[issues#374](https://github.com/MaskRay/ccls/issues/374)。所以建议用vs的相关工具进行build。

&emsp;&emsp;另外要注意，使用git克隆llvm仓库的时候有一个多G，需要网络良好，配置ok，可以用清华镜像源。

### 2.5 markdown相关

&emsp;&emsp;参考我的另一篇博客配置。

## 三、一些细碎的点

- 可视模式下的**o/O**键可以切换光标位置
- VIM正则表达式懒惰模式：`\{-}`，参考[VIM中正则的非贪婪匹配](http://blog.sina.com.cn/s/blog_3cf5c5ca0100wfmw.html)

