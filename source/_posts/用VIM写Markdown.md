---
title: 用VIM写Markdown
date: 2021-02-19 16:22:17
categories: 记录
tags: [Vim]
description: 使用VIM作为编辑器之后，再拿Typora写Markdown时感觉到了一些别扭，故打算配置一下VIM，以后用VIM来写Markdown。
keywords: 
cover: false
---
# 用VIM写Markdown


&emsp;&emsp;使用VIM作为编辑器之后，再拿Typora写Markdown时感觉到了一些别扭，故打算配置一下VIM，以后用VIM来写Markdown。当然，VIM和Typora各有千秋，个人认为VIM更侧重文本编辑，Typora更侧重功能性完整性。


## [vim-markdown](https://github.com/plasticboy/vim-markdown)

- 安装：`Plug 'plasticboy/vim-markdown', {'for':'markdown'} `
- 配置：
  ```javascript
	" --------------------------- vim-markdown start -------------------------- "
	" Folding headers
	let g:vim_markdown_folding_disabled = 1
	" let g:vim_markdown_folding_style_pythonic = 1
	" let g:vim_markdown_folding_level = 2
  
	" Concealing syntax
	set conceallevel=2
	let g:vim_markdown_conceal_code_blocks = 0
  
	" Syntax Extensions
	let g:vim_markdown_strikethrough = 1
	let g:vim_markdown_math = 1
  
	" Follow named anchors: can use `ge` command to jump
	let g:vim_markdown_follow_anchor = 1
  
	" Toc window autofit
	let g:vim_markdown_toc_autofit = 1
  
	" Bullets and indent
	let g:vim_markdown_auto_insert_bullets = 0
	let g:vim_markdown_new_list_item_indent = 0
  
	" --------------------------- vim-markdown end -------------------------- "
  ```
- 使用：
  - `gx`: 用浏览器打开光标下的链接，光标在整个`[Text](Url)`都有效
  - `ge`: 在VIM中打开光标下的链接来编辑，一般是Toc
  - `]]`: 跳转到下一个header
  - `[[`: 跳转到上一个header
  - `][`: 跳转到下一个sibling header
  - `[]`: 跳转到上一个sibling header
  - `]c`: 跳转到当前header
  - `]u`: 跳转到父级header
  - `:TableFormat`：格式化当前光标下的表格，**需要使用Tabular插件**。
  - `:Toc`：打开一个quickfix垂直窗口显示headers的导航表
  - `:InsertToc`：在当前行插入Toc表，可以跟一个数字参数表示展示多少级的headers
  - `:InsertNToc`：和`:InsertToc`一样，不过内容表中的`h2`标题是有序列表



## [markdown-preview.nvim](https://github.com/iamcco/markdown-preview.nvim)

&emsp;&emsp;用浏览器实时预览Markdown文件的VIM插件我尝试了两个，另一个效果不太好

- 安装这个插件要先装好node.js和yarn，yarn用npm安装即可

- 有一个需要注意的点是，有时候解析markdown的时候会把`<...>`这样的解析成html语法导致预览结果与预期不一致，记得转义就好了。

- 配置：
  ```javascript
  Plug 'iamcco/markdown-preview.nvim', { 'do': 'cd app && yarn install', 'for':'markdown'}
  
  " --------------------------- mardown-preview start -------------------------- "
  " Auto Start or Close
  " let g:mkdp_auto_start = 1
  let g:mkdp_auto_close = 1
  
  " Specify markdown or highlight css file
  " let g:mkdp_markdown_css = 'C:\Path\To\Typora\themes\mo.css'
  let g:mkdp_highlight_css = expand('$VIM\vimfiles\highlight.js\src\styles\atom-one-dark.css')
  
  " Command
  autocmd FileType markdown nnoremap <F8> :MarkdownPreview<CR>
  autocmd FileType markdown inoremap <F8> <ESC>:MarkdownPreview<CR>
  autocmd FileType markdown nnoremap <F9> :MarkdownPreviewStop<CR>
  autocmd FileType markdown inoremap <F9> <ESC>:MarkdownPreviewStop<CR>
  " autocmd Filetype markdown noremap ,b i****<Esc>hi
  
  " --------------------------- mardown-preview end -------------------------- "
  
  ```
- 编辑markdown文件的时候在插入模式或普通模式下按F8即可打开浏览器实时预览，再按F9即可关闭，可以自定义。因为设置了自动关闭，所以离开markdown这个buffer的时候实时预览也会自动关闭。
- 浏览器渲染文档的方式可自定义，也就是配置项中的两个css文件。markdown渲染默认的就可以了，代码高亮css是选用了[highlight.js](https://github.com/highlightjs/highlight.js)中的主题。如果按我的配置使用atom-one-dark的代码高亮和默认的markdown渲染css的话，需要找到`"$Vim\vimfiles\markdown-preview.nvim\app\_static\markdown.css"`，在最后添加
  ```css
  .markdown-body pre {
    background-color: #000;
  }
  ```
  




## [Ultisnips](https://github.com/SirVer/ultisnips)

&emsp;&emsp;这个插件其实不止作用于markdown，更常用于打代码的时候，也就是代码片段。

- 首先需要python环境，VIM要支持的同时，系统还要有与VIM`:version`命令中指定的python版本一致的python环境，并配置在环境变量里。
- 配置：
  ```javascript
  Plug 'SirVer/ultisnips'
  
  " --------------------------- Ultisnips start -------------------------- "
  " Python3 Env
  " set pythonthreedll=/c/Program\ Files/Python36/python36.dll
  
  " Disable SnipMate
  let g:UltiSnipsEnableSnipMate = 0
  
  " Key Map
  let g:UltiSnipsExpandTrigger = '<tab>'
  let g:UltiSnipsListSnippets = '<c-tab>'
  let g:UltiSnipsJumpForwardTrigger = '<tab>' 
  let g:UltiSnipsJumpBackwardTrigger = '<S-tab>' 
  
  " Snippet files Search Dir
  let g:UltiSnipsSnippetDirectories=['c:\Path\To\vimfiles\ultisnips']
  
  " Snippet Edit Window
  let g:UltiSnipsEditSplit="vertical"
  " --------------------------- Ultisnips end -------------------------- "
  ```
- 关于配置项这里有个坑点，在指定snippets文件(*.snippets)搜索目录的时候，**不能把最后一级目录名指定为`snippets`**，这样的话Ultispits会认为该目录下的snippets文件是SnipMate格式的，就会出现snippets定义语法错误的问题。参见[issue1316](https://github.com/SirVer/ultisnips/issues/1316)



----------

&emsp;&emsp;总之，把这几个插件都配置好之后，拿VIM写Markdown已经可以满足基本需求了。VIM比起Typora来说，虽然看起来没有那么便捷、美观，但强大的编辑操作以及高度可定制性足以带来极佳的体验。


## 参考

- [高效做笔记:vim + markdown](https://zhuanlan.zhihu.com/p/84773275)
- [Snippets参考1](https://github.com/cold-soda-jay/Markdown-vim/blob/master/markdown.snippets)
- [Snippets参考2](https://github.com/liangxianzhe/oh-my-vim/tree/master/UltiSnips/markdown.snippets)
- [Snippets参考3](https://github.com/honza/vim-snippets/blob/master/UltiSnips/markdown.snippets)
- [VIM 代码片段插件 ultisnips 使用教程](https://segmentfault.com/a/1190000016194775)

