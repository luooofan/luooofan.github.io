---
title: 用Markdown写PPT
date: 2021-10-18 16:29:42
description: 介绍了利用Markdown写PPT的两种方案，同时附了Typora更换代码块高亮主题的方法和VSCode Markdown预览样式美化的方法。
categories: Tool
tags: 记录
cover: false
---

# 用Markdown写PPT

## 方案一：[Marp](https://marp.app/)

可以在VSCode上下个插件直接用，预览的时候会直接显示成PPT的样子，可以导出pdf、pptx等多种格式。

使用非常简单，可以过一遍文档。

简单来说就是以下几点：

- 写md文档的时候在yaml front matter中指定`marp: true`即启用
- 使用`---`来做ppt页分割
- 有一些**Directives**可用，可以在 yaml front matter 和用 html 注释语法来配置一些选项
- 拓展了Markdown图片语法：`![](image.jpg)`
- 官方内置了三套主题，另外主题CSS可自定义

[可以参考的一份ppt：k8s-share](https://github.com/jeremyxu2010/k8s-share)



## 方案二：[reveal.js](https://revealjs.com/)

还没用，等以后补吧





## 附1：Typora更换代码块高亮主题

Typora的代码块语法高亮使用的是[CodeMirror](https://codemirror.net/)实现的，所以需要更换代码块的样式，可以去CodeMirror提供的[主题](https://codemirror.net/theme/)里下载样式表文件。

![image-20211018163915785](http://img.luooofan.site/20211018-182633-image-20211018163915785.png)

在这里选Themes即可跳到代码块样式展示界面。

下载想要的主题对应的样式表文件，放到Typora主题文件夹中，把css文件中所有的`.cm-s-themename`改为`.cm-s-inner`，然后重启Typora即可选择相应的代码块高亮主题。

这种情况只是修改了代码块高亮的方式，其他Markdown特性的渲染效果仍为默认效果，想更定制化可以自行制作css样式文件。

（在[Typora Themes](https://theme.typora.io/)或者一些社区可以找到别人制作的主题然后下载使用，我目前用的是[Mo](https://theme.typora.io/theme/Mo/))

参考：

- [Typora 修改代码块高亮样式](https://www.cnblogs.com/Anber82/p/14120414.html)
- [「玩物志」精美而强大的Markdown编辑器 - Typora](https://ruterly.com/2017/01/10/Typora/)



## 附2：VSCode Markdown预览样式美化

**可以使用`markdown-preview-enhanced`这个插件**

**可以配置settings.json**：[官方文档](https://code.visualstudio.com/docs/languages/markdown#_using-your-own-css)，本地文件的话应该要用相对路径或者文件url

Marp VSCode插件也支持使用自定义css样式文件

```json
{
  "markdown.marp.themes": [
    "./themes/mo.css"
  ],
  "markdown.styles": [
    "./themes/atom-one-dark.css"
  ],
  "markdown.trace": "verbose"
}
```

VSCode Markdown代码块高亮使用的是[highlight.js](https://highlightjs.org/)，所以也可以配置使用其他的代码块高亮主题，在[demo](https://highlightjs.org/static/demo/)选择看中的主题

如果使用Marp时要更换代码块高亮主题，可以在md文件YAML front matter部分写`style`，把主题css文件中的内容拷进去，并且在`.hljs`前加上`section`

附上我在使用Marp Gaia主题时自定义的代码块高亮格式，是基于[base16/gruvbox-light-soft](https://github.com/highlightjs/highlight.js/blob/main/src/styles/base16/gruvbox-light-soft.css)简单修改的，感觉和Gaia更搭：

```yaml
marp: true
theme: gaia
footer: luooofan
paginate: true
style: |
    section {
        font-size: 30px;
    }
    section pre code .hljs {
        display: block;
        overflow-x: auto;
        padding: 1em;
    }

    section code .hljs {
        padding: 3px 5px;
    }

    section pre code {
        color: #504945;
        background: #f2e5bc;
    }

    section .hljs::selection,
    section .hljs ::selection {
        background-color: #d5c4a1;
        color: #504945;
    }


    /* purposely do not highlight these things */
    section .hljs-formula,
    section .hljs-params,
    section .hljs-property
    {}

    /* base03 - #bdae93 -  Comments, Invisibles, Line Highlighting */
    section .hljs-comment {
        color: #9b8d74;
    }

    /* base04 - #665c54 -  Dark Foreground (Used for status bars) */
    section .hljs-tag {
        color: #665c54;
    }

    /* base05 - #504945 -  Default Foreground, Caret, Delimiters, Operators */
    section .hljs-subst,
    section .hljs-punctuation,
    section .hljs-operator {
        color: #504945;
    }

    section .hljs-operator {
        opacity: 0.7;
    }

    /* base08 - Variables, XML Tags, Markup Link Text, Markup Lists, Diff Deleted */
    section .hljs-bullet,
    section .hljs-variable,
    section .hljs-template-variable,
    section .hljs-selector-tag,
    section .hljs-name,
    section .hljs-deletion {
        color: #9d0006;
    }

    /* base09 - Integers, Boolean, Constants, XML Attributes, Markup Link Url */
    section .hljs-symbol,
    section .hljs-number,
    section .hljs-link,
    section .hljs-attr,
    section .hljs-variable.constant_,
    section .hljs-literal {
        color: #af3a03;
    }

    /* base0A - Classes, Markup Bold, Search Text Background */
    section .hljs-title,
    section .hljs-class section .hljs-title,
    section .hljs-title.class_
    {
        color: #b57614;
    }

    section .hljs-strong {
        font-weight:bold;
        color: #b57614;
    }

    /* base0B - Strings, Inherited Class, Markup Code, Diff Inserted */
    section .hljs-code,
    section .hljs-addition,
    section .hljs-title.class_.inherited__,
    section .hljs-string {
        color: #79740e;
    }

    /* base0C - Support, Regular Expressions, Escape Characters, Markup Quotes */
    section .hljs-built_in,
    section .hljs-doctag, /* guessing */
    section .hljs-quote,
    section .hljs-keyword
    section .hljs-atrule,
    section .hljs-regexp {
        color: #427b58;
    }

    /* base0D - Functions, Methods, Attribute IDs, Headings */
    section .hljs-function section .hljs-title,
    section .hljs-attribute,
    .ruby section .hljs-property,
    section .hljs-title.function_,
    section .hljs-section {
        color: #076678;
    }

    /* base0E - Keywords, Storage, Selector, Markup Italic, Diff Changed */
    section .hljs-type,
    /* section .hljs-selector-id, */
    /* section .hljs-selector-class, */
    /* section .hljs-selector-attr, */
    /* section .hljs-selector-pseudo, */
    section .hljs-template-tag,
    .diff section .hljs-meta,
    section .hljs-keyword {
        color: #8f3f71;
    }
    section .hljs-emphasis {
        color: #8f3f71;
        font-style: italic;
    }

    /* base0F - Deprecated, Opening/Closing Embedded Language Tags, e.g. <?php ?> */
    section .hljs-meta,
    /*
    prevent top level .keyword and .string scopes
    from leaking into meta by accident
    */
    section .hljs-meta section .hljs-keyword,
    section .hljs-meta section .hljs-string
    {
        color: #d65d0e;
    }

    section .hljs-meta section .hljs-keyword,
    /* for v10 compatible themes */
    section .hljs-meta-keyword {
        font-weight: bold;
    }
```

效果如图：

![image-20211018172308726](https://img.luooofan.site/20211018-173511-image-20211018172308726.png)

（做了个同样式的模板md文件放在[Github Gist](https://gist.github.com/luooofan/5c6154a31191aa3cd6c88e48b3f051b8)了）



另外，Typora和VSCode都可以用**开发人员工具**查看渲染的结果，可以选择相应元素查看对应标签和样式，可以在控制台看报错信息

