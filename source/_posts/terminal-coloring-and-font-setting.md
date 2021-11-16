---
title: 终端颜色和字体设置
comments: true
date: 2021-11-16 14:08:20
categories: [Server, 记录]
tags: [Server, 记录, Linux, Vim]
description: 256色终端、真彩色终端的设置，tmux和vim开启true-color支持，vim光标的一些问题以及选用的编程字体
keywords:
cover: false
---



## 从改变终端输出文本的颜色说起

参考[控制台终端输出颜色](https://www.cnblogs.com/opangle/p/4082692.html)，[使用 Win10 命令提示符自带命令实现彩色输出](https://c1ino.github.io/c1ino/2019/08-03-colored-echo-in-win10-command-prompt)

当我们想改变输出到控制台终端的文本的颜色时，可以这样：

```bash
echo -e "\033[31mRed Text\033[0m"
echo -e "\033[32mGreen Text\033[0m"
echo -e "\033[33mYellow Text\033[0m"
echo -e "\033[34mBlue Text\033[0m"
echo -e "\033[35mMagenta Text\033[0m"
echo -e "\033[36mCyan Text\033[0m"
```

![image-20211116160551691](http://img.luooofan.site/20211116-210854-image-20211116160551691.png)

其中：`\033[31m`、`\033[31m`、`\033[0m`等是 **ANSI转义序列**（ANSI escape code/sequence），它控制文本输出的格式、颜色等。

NOTE: `\033`是键盘左上角 ESC 键对应的 ASCII 码（8进制），`\033`、`\x1b`和`\e`还有`^[`效果一样

参考[ASCII_escape_character](https://en.wikipedia.org/wiki/Escape_character#ASCII_escape_character)，[How do I pass ESC to stdin?](https://stackoverflow.com/questions/31304939/how-do-i-pass-esc-to-stdin)，[shell脚本中键盘上下左右和ESC键的控制](https://blog.csdn.net/weixin_44901564/article/details/102738916)



## [ANSI转义序列](https://en.wikipedia.org/wiki/ANSI_escape_code)

前面提到了 ANSI 转义序列，并且提到它不仅仅能控制文本颜色，还能控制格式等

```bash
echo -e "\033[3mitalic\033[23m"
```

![image-20211116161320160](http://img.luooofan.site/20211116-210855-image-20211116161320160.png)



事实上，`\033[nm`也只是 ANSI 转义码中的一种格式，可以在[wiki: ANSI_escape_code](https://en.wikipedia.org/wiki/ANSI_escape_code)看到更多的转义码

其他参考[console_codes(4) — Linux manual page](https://man7.org/linux/man-pages/man4/console_codes.4.html)，[Console Virtual Terminal Sequences](https://docs.microsoft.com/en-us/windows/console/console-virtual-terminal-sequences)

## 终端颜色

可以先看一下这篇[TrueColor](https://gist.github.com/XVilka/8346728)

默认配置下应该 256 色终端类型较多，不过我们更期望 24bit 真彩色（true color）的终端

相关的几个Linux环境变量：`$TERM`，`$COLORTERM`

Linux有一个`terminfo`数据库，结合`tput`命令可以看到支持的终端类型及其颜色种类：

```bash
for T in `find /usr/share/terminfo -type f -printf '%f '`;do echo "$T `tput -T $T colors`";done| sort -nk2 | tail -n20
```

如果`echo $TERM`发现只是`xterm`的话，默认是8色的，可以用`tput colors`验证

```bash
# 配置为256色
export TERM=xterm-256color
# 配置支持true color
export COLORTERM=truecolor
```

验证终端是否支持真彩色的一种方法：

```bash
awk 'BEGIN{
    s="/\\/\\/\\/\\/\\"; s=s s s s s s s s;
    for (colnum = 0; colnum<77; colnum++) {
        r = 255-(colnum*255/76);
        g = (colnum*510/76);
        b = (colnum*255/76);
        if (g>255) g = 510-g;
        printf "\033[48;2;%d;%d;%dm", r,g,b;
        printf "\033[38;2;%d;%d;%dm", 255-r,255-g,255-b;
        printf "%s\033[0m", substr(s,colnum+1,1);
    }
    printf "\n";
}'
```

若支持，如下图，不支持的话则是断断续续的

![image-20211116163135753](http://img.luooofan.site/20211116-210856-image-20211116163135753.png)



参考[判断终端色彩支持情况的方法](https://gaomf.cn/2017/01/16/Terminal_Color/)，[zsh true color disabled on WSL2?](https://askubuntu.com/questions/1309068/zsh-true-color-disabled-on-wsl2)，[2.1.1 Terminal emulator programs](https://www.gnu.org/software/gettext/libtextstyle/manual/html_node/Terminal-emulators.html)，[Color output in console](https://wiki.archlinux.org/title/Color_output_in_console)

## 为tmux和vim开启true-color支持

### Vim

`vim >= 7.4.1770` 及 `neovim >= 0.2.2` 都支持真彩色

`:help xterm-true-color`

修改配置文件`~/.vimrc`

```bash
if has("termguicolors")
    " fix bug for vim
    set t_8f=^[[38;2;%lu;%lu;%lum
    set t_8b=^[[48;2;%lu;%lu;%lum

    " enable true color
    set termguicolors
endif
```

**NOTE**：`^[` 这里是单个 ESC 键，可以先按 `Ctrl-V` 再按 `ESC` 键键入

配置完后可以重新打开vim，`:terminal` 打开终端，测试 true-color

### Tmux

修改配置文件`~/.tmux.conf`

```bash
set -g default-terminal "tmux-256color"
set-option -ga terminal-overrides ",*256col*:Tc"
```

**NOTE**：

- Tc 即代表 true-color 支持
- 默认终端还可以设置为`screen-256color`，但是这个终端类型不支持**斜体**，所以在tmux里使用vim时就会导致原本被设置为斜体的部分显示异常，可以参考[gruvbox：Comments looking strange withing tmux](https://github.com/morhetz/gruvbox/issues/43)，[在 tmux 环境中使用 tmux-256color](https://zhuanlan.zhihu.com/p/293788971)，[为tmux和vim开启斜体和真彩色](https://blog.csdn.net/ecnu_lzj/article/details/104455702)

配置完后退出所有的tmux，重新打开，测试 true-color 即可

参考[Tmux 24bit True Color Support](https://ttys3.dev/post/tmux-24bit-true-color-support/)，[tmux 与 vim 开启 true-color 支持](https://www.ogura.io/posts/2018/09/true-color-in-vim-under-tmux-on-ubuntu/)



## 编程字体

VSCode中可以配置多种字体，Windows Terminal中目前只能配置单字体

尝试了多种字体：[Source Code Pro](https://github.com/adobe-fonts/source-code-pro)，[Fira Code](https://github.com/tonsky/FiraCode)，[ JetBrains Mono](https://github.com/JetBrains/JetBrainsMono)，目前是选择最后一个在用

中文字体尝试了：[source-han-sans 思源黑体](https://github.com/adobe-fonts/source-han-sans)，[Sarasa-Gothic 更纱黑体](https://github.com/be5invis/Sarasa-Gothic)，目前在用后者

VSCode当前字体相关配置：

```json
{
	"editor.fontSize": 18,
    "editor.lineHeight": 30,
    "editor.fontFamily": "'JetBrains Mono','Sarasa Mono SC'",
    "editor.fontLigatures": true,
    "[markdown]": {
        "editor.fontFamily": "'Sarasa Mono SC'"
    },
}
```

[字体分享](https://fonts.google.com/share?selection.family=Fira%20Code%7CInconsolata%7CJetBrains%20Mono:ital,wght@0,300;1,300%7CSource%20Code%20Pro:ital@0;1)

[一个支持多种字体多种编程语言的预览网站](https://www.programmingfonts.org/#jetbrainsmono)



*附几个vscode trick*：

- [修改 vscode 中主题注释中文的斜体格式](https://blog.csdn.net/weixin_43918847/article/details/111116697)
- [Disable italic syntax highlighting in VS Code](https://stackoverflow.com/questions/44219768/disable-italic-syntax-highlighting-in-vs-code)
- [VS Code 中英文字体对齐](https://blog.lrtfm.com/2020/05/2020-05-07-vscode-zh-font/)
- [VSCode 等宽字体之坑](https://moe.best/gotagota/vscode-monospaced.html)

## Vim光标

在gvim下选用gruvbox主题时光标表现良好

先附上相关的帮助文档

`:help termcap-cursor-color`

[Vim中文文档：term.txt](https://yianwillis.github.io/vimcdoc/doc/term.html#builtin-terms)

### 问题一：WT内使用Vim时光标样式不同模式下无差别

Solved Issue：[Vim cursor can't be changed](https://github.com/microsoft/terminal/issues/4335)

```c
" modify the cursor style in wsl
" LINK: https://github.com/microsoft/terminal/issues/4335
" NOTE: 0 doesn't work. 1 works well.
if &term =~ '^xterm'
  " enter vim
  autocmd VimEnter * silent !echo -ne "\e[1 q"
  " oherwise
  let &t_EI .= "\<Esc>[1 q"
  " let &t_EI .= "\<Esc>]12;blue\x7"
  " blue can also: rgb:xx/xx/xx
  " insert mode
  let &t_SI .= "\<Esc>[5 q"
  " 1 or 0 -> blinking block
  " 2 -> solid block
  " 3 -> blinking underscore
  " 4 -> solid underscore
  " Recent versions of xterm (282 or above) also support
  " 5 -> blinking vertical bar
  " 6 -> solid vertical bar
  " leave vim
  autocmd VimLeave * silent !echo -ne "\e[5 q"
endif
```

### 问题二：tmux内光标样式显示不正确

```c
" tmux will only forward escape sequences to the terminal if surrounded by a DCS sequence                               if exists('$TMUX')
   " set insert mode to a cyan vertical line
   let &t_SI .= "\<esc>Ptmux;\<esc>\<esc>[6 q\<esc>\\"
   " let &t_SI .= "\<esc>Ptmux;\<esc>\<esc>]12;cyan\x7\<esc>\\"
   " set normal mode to a green block
   let &t_EI .= "\<esc>Ptmux;\<esc>\<esc>[2 q\<esc>\\"
   " let &t_EI .= "\<esc>Ptmux;\<esc>\<esc>]12;green\x7\<esc>\\"
   " set replace mode to an orange underscore
   let &t_SR .= "\<esc>Ptmux;\<esc>\<esc>[4 q\<esc>\\"
   " let &t_SR .= "\<esc>Ptmux;\<esc>\<esc>]12;orange\x7\<esc>\\"

   " initialize cursor shape/color on startup (silent !echo approach doesn't seem to work for tmux)
   augroup ResetCursorShape
      au!
      "autocmd VimEnter * startinsert | stopinsert
      autocmd VimEnter * normal! :startinsert :stopinsert
      "autocmd VimEnter * :normal :startinsert :stopinsert
   augroup END

   " reset cursor when leaving tmux
   autocmd VimLeave * silent !echo -ne "\033Ptmux;\033\033[2 q\033\\"
   autocmd VimLeave * silent !echo -ne "\033Ptmux;\033\033]12;gray\007\033\\"
endif
```

参考[Configuring the cursor](https://vim.fandom.com/wiki/Configuring_the_cursor)

### 问题三：使用Vim时光标颜色显示不理想

**NOTE!!!**：在终端内使用vim的话不要试图使用`:hi Cursor`来更改光标格式

[Why is :highlight Cursor ... ignored?](https://vi.stackexchange.com/questions/7176/why-is-highlight-cursor-ignored)

> Playing with the `Cursor` highlight group is a waste of time because the cursor color is handled by your terminal emulator, not by Vim.

因为光标颜色是由终端模拟器而不是Vim来处理的

Vim可以设置一些终端选项来处理光标颜色：[Cursor color in xterm; change accordingly to the syntax in vim](https://stackoverflow.com/questions/14920634/cursor-color-in-xterm-change-accordingly-to-the-syntax-in-vim)，其实和前面改光标样式一样

然后WT虽然在这方面做了一些工作但还没有达到理想：

- [Cursor Text Foreground Color](https://github.com/microsoft/terminal/blob/24781bc6ddec86b18c2ee6225e2bbc1cd9c55fac/doc/specs/%231203%20-%20Cursor%20Text%20Color.md)
- [Cannot see cursor on white background - should have an invert option](https://github.com/microsoft/terminal/issues/7118)



参考[Vim 配置光标形状和颜色（Windows Terminal、xterm）](https://www.starky.ltd/2021/06/08/change-vim-cursor-shape-and-color-in-xterm-or-windows-terminal/)，[[Vim Tips]设置Windows Terminal内vim的光标](https://www.littlezhang.com/2020/11/vim-tips%E8%AE%BE%E7%BD%AEwindows-terminal%E5%86%85vim%E7%9A%84%E5%85%89%E6%A0%87/)，[Is it possible to reverse an existing highlighting group?](https://vi.stackexchange.com/questions/21546/is-it-possible-to-reverse-an-existing-highlighting-group)，[How can I change cursor color in VSCode?](https://stackoverflow.com/questions/50200924/how-can-i-change-cursor-color-in-vscode)，[Better Understanding of Vim colors](https://dev.to/mkuts12/better-understanding-of-vim-colors-5e81)

