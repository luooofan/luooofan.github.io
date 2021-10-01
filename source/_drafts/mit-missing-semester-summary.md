---
title: mit-missing-semester-summary
tags:
---

# MIT missing semester

只记录一些对于我本人来说值得记录的内容，多是一些零散的点和相关资料

## Course overview + the shell

- Shell本身即是一个程序，接收我们输入的命令，经过一些处理之后（比如解析输入串、通过`$PATH`找到可执行文件所在的位置等等）开始执行

- Shell中某些字符是有特殊意义的，如**\$**，**\`**等，当想要符号本身的字面量时可以用[Quoting](https://www.gnu.org/software/bash/manual/html_node/Quoting.html)。在单引号中包裹的全部字符都会保留其字面值。那也意味着其中不允许出现单引号，用转义符也不可

- `cd -`可以在上一个目录与当前目录之间切换

- [shebang](https://en.wikipedia.org/wiki/Shebang_(Unix))可以指定文件的执行方式，`#! /usr/bin/env python`，`env` 会利用`PATH` 环境变量来进行定位

- Linux下的文件权限：**rwxXst**

  ![image-20210925195246890](G:\GeneralData\Pictures\typoraphoto\mit-missing-semester-summary\image-20210925195246890.png)

- [sysfs 文件系统与Linux设备模型](https://blog.csdn.net/zqixiao_09/article/details/50864489)



## Shell Tools and Scripting

- 在Shell中可以把空格看作一个保留字符，用于分割参数，所以写Shell脚本的时候需要注意空格，如：`foo=bar`

- **$_**：最后一个参数

- **!!**：上一条执行的命令

- Command Substitution：`foo=$(pwd)`

- Process Substitution：`cat <(ls) <(ls ..)`

   `<( CMD )` 会执行 `CMD` 并将结果输出到一个临时文件中，并将 `<( CMD )` 替换成临时文件名。

- Bash中实现了很多比较操作，可以查看**test**手册

- 在bash中进行比较时，尽量使用双方括号 `[[ ]]` 而不是单方括号 `[ ]`，这样会降低犯错的几率，尽管这样并不能兼容 `sh`。 更详细的说明参见[这里](http://mywiki.wooledge.org/BashFAQ/031)

- [Advanced Bash-Scripting Guide:Special Characters](https://tldp.org/LDP/abs/html/special-chars.html)

- **shellcheck**：shell script检查

- **convert & ffmpeg** 分别处理图像和视频

- **tldr**：一些命令的样例  [TLDR pages](https://tldr.sh/) 

- `find . -name "\*.tmp" -exec rm {} \;`

- **locate**，数据库索引 加快查找速度 **updatedb**

- `rg "import requests" -t py -C 5 ~/scratch`

  `rg -u --files-without-match "^#\!" -t sh`

- Ctrl-R：command backward search

- **fzf**：fuzzy finder

  `[ -f ~/.fzf.zsh ] && source ~/.fzf.zsh`

- **tree  broot  nnn**



## Editors(Vim)

- 授课教师的Vim设置 ([Anish](https://github.com/anishathalye/dotfiles/blob/master/vimrc), [Jon](https://github.com/jonhoo/configs/blob/master/editor/.config/nvim/init.vim) (uses [neovim](https://neovim.io/)), [Jose](https://github.com/JJGO/dotfiles/blob/master/vim/.vimrc))

### 扩展Vim

- [Vim Awesome](https://vimawesome.com/)
- [ctrlp.vim](https://github.com/ctrlpvim/ctrlp.vim): 模糊文件查找
- [ack.vim](https://github.com/mileszs/ack.vim): 代码搜索
- [nerdtree](https://github.com/scrooloose/nerdtree): 文件浏览器
- [vim-easymotion](https://github.com/easymotion/vim-easymotion): 魔术操作

### 其他程序的Vim模式

- Shell：如果你是一个 Bash 用户，用 `set -o vi`。如果你用 Zsh：`bindkey -v`（`setopt vi`）。Fish 用 `fish_vi_key_bindings`。另外，不管利用什么 shell，你可以 `export EDITOR=vim`。

- 很多程序使用 [GNU Readline](https://tiswww.case.edu/php/chet/readline/rltop.html) 库来作为 它们的命令控制行界面。Readline 也支持基本的 Vim 模式， 可以通过在 `~/.inputrc` 添加如下行开启：`set editing-mode vi`

  比如，在这个设置下，Python REPL 会支持 Vim 快捷键。

- 甚至有 Vim 的网页浏览快捷键 [browsers](http://vim.wikia.com/wiki/Vim_key_bindings_for_web_browsers), 受欢迎的有 用于 Google Chrome 的 [Vimium](https://chrome.google.com/webstore/detail/vimium/dbepggeogbaibhgnhhndojpepiihcmeb?hl=en) 和用于 Firefox 的 [Tridactyl](https://github.com/tridactyl/tridactyl)。 你甚至可以在 [Jupyter notebooks](https://github.com/lambdalisue/jupyter-vim-binding) 中用 Vim 快捷键。 [这个列表](https://reversed.top/2016-08-13/big-list-of-vim-like-software) 中列举了支持类 vim 键位绑定的软件。

### 拓展资料

- `vimtutor` 是一个 Vim 安装时自带的教程
- [Vim Adventures](https://vim-adventures.com/) 是一个学习使用 Vim 的游戏
- [Vim Tips Wiki](http://vim.wikia.com/wiki/Vim_Tips_Wiki)
- [Vim Advent Calendar](https://vimways.org/2019/) 有很多 Vim 小技巧
- [Vim Golf](http://www.vimgolf.com/) 是用 Vim 的用户界面作为程序语言的 [code golf](https://en.wikipedia.org/wiki/Code_golf)
- [Vi/Vim Stack Exchange](https://vi.stackexchange.com/)
- [Vim Screencasts](http://vimcasts.org/)
- [Practical Vim](https://pragprog.com/titles/dnvim2/)（书籍）

### 课后练习

用 Vim 宏将 XML 转换到 JSON ([例子文件](https://missing-semester-cn.github.io/2020/files/example-data.xml))。 参考[宏](https://missing-semester-cn.github.io/2020/editors/#macros) 章节。



## Data Wrangling





## Comman-line Environment















## References

- [The Missing Semester of Your CS Education](https://missing.csail.mit.edu/)
- [B站视频](https://www.bilibili.com/video/BV1x7411H7wa)
- 

