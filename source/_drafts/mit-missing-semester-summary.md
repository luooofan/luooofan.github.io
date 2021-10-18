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

授课教师的Vim设置 ([Anish](https://github.com/anishathalye/dotfiles/blob/master/vimrc), [Jon](https://github.com/jonhoo/configs/blob/master/editor/.config/nvim/init.vim) (uses [neovim](https://neovim.io/)), [Jose](https://github.com/JJGO/dotfiles/blob/master/vim/.vimrc))

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

- `ssh myserver 'journalctl | grep sshd | grep "Disconnected from"' | sed 's/.*Disconnected from //'`

- `perl -pe 's/.*?Disconnected from //'`，perl 的命令行模式可以给 `*` 或 `+` 增加一个`?` 后缀使其变成非贪婪模式

- 正则表达式在线调试工具[regex debugger](https://regex101.com/r/qqbZqh/2)

- 为了完成某种匹配，我们最终可能会写出非常复杂的正则表达式。例如，这里有一篇关于如何匹配电子邮箱地址的文章[e-mail address](https://www.regular-expressions.info/email.html)，匹配电子邮箱可一点[也不简单](https://emailregex.com/)。网络上还有很多关于如何匹配电子邮箱地址的[讨论](https://stackoverflow.com/questions/201323/how-to-validate-an-email-address-using-a-regular-expression/1917982)。人们还为其编写了[测试用例](https://fightingforalostcause.net/content/misc/2006/compare-email-regex.php)及 [测试矩阵](https://mathiasbynens.be/demo/url-regex)。您甚至可以编写一个用于判断一个数[是否为质数](https://www.noulakaz.net/2007/03/18/a-regular-expression-to-check-for-prime-numbers/)的正则表达式

- ```bash
  ssh myserver journalctl
   | grep sshd
   | grep "Disconnected from"
   | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
   | sort | uniq -c
   | sort -nk1,1 | tail -n10
   | awk '{print $2}' | paste -sd,
  ```
  `sort` 会对其输入数据进行排序。`uniq -c` 会把连续出现的行折叠为一行并使用出现次数作为前缀。
  
  `sort -n` 会按照数字顺序对输入进行排序（默认情况下是按照字典序排序 `-k1,1` 则表示“仅基于以空格分割的第一列进行排序”。`,n` 部分表示“仅排序到第n个部分”，默认情况是到行尾。
  
  使用`sort -r`来进行倒序排序。
  
  可以利用 `paste`命令来合并行(`-s`)，并指定一个分隔符进行分割 (`-d`)
  
- `awk` 其实是一种编程语言，只不过它碰巧非常善于处理文本。

-  `{print $2}` 的作用是什么？ `awk` 程序接受一个模式串（可选），以及一个代码块，指定当模式匹配时应该做何种操作。默认当模式串即匹配所有行（上面命令中当用法）。 在代码块中，`$0` 表示整行的内容，`$1` 到 `$n` 为一行中的 n 个区域，区域的分割基于 `awk` 的域分隔符（默认是空格，可以通过`-F`来修改）。在这个例子中，我们的代码意思是：对于每一行文本，打印其第二个部分，也就是用户名。

- 让我们统计一下所有以`c` 开头，以 `e` 结尾，并且仅尝试过一次登陆的用户。

  `awk '$1 == 1 && $2 ~ /^c[^ ]*e$/ { print $2 }' | wc -l`

  ```bash
  BEGIN { rows = 0 }
  $1 == 1 && $2 ~ /^c[^ ]*e$/ { rows += $1 }
  END { print rows }
  ```
  `BEGIN` 也是一种模式，它会匹配输入的开头（ `END` 则匹配结尾）。然后，对每一行第一个部分进行累加，最后将结果输出。
  
- ```bash
  # 数学计算
  echo "2*($(data | paste -sd+))" | bc -l
  
  # R语言 summary 可以打印某个向量的统计结果
   | awk '{print $1}' | R --slave -e 'x <- scan(file="stdin", quiet=TRUE); summary(x)'
   
  # 绘制一些简单的图表， 可以用gnuplot 
   | gnuplot -p -e 'set boxwidth 0.5; plot "-" using 1:xtic(2) with boxes'
  ```

- `rustup toolchain list | grep nightly | grep -vE "nightly-x86" | sed 's/-x86.*//' | xargs rustup toolchain uninstall`

- 整理二进制数据

  可以用 ffmpeg 从相机中捕获一张图片，将其转换成灰度图后通过SSH将压缩后的文件发送到远端服务器，并在那里解压、存档并显示。

  ```bash
  ffmpeg -loglevel panic -i /dev/video0 -frames 1 -f image2 -
   | convert - -colorspace gray -
   | gzip
   | ssh mymachine 'gzip -d | tee copy.jpg | env DISPLAY=:0 feh -'
  ```



## Comman-line Environment

### Job control

-  shell 会使用 UNIX 提供的信号机制执行进程间通信：`man signal` `man kill`

  当一个进程接收到信号时，它会停止执行、处理该信号并基于信号传递的信息来改变其执行。就这一点而言，信号是一种*软件中断*。

  有些信号能被软件捕获，比如 SIGINT， 有些不能，比如 SIGKILL

- `Ctrl-C: SIGINT` `Ctrl-\: SIGQUIT`

  ```python
  #!/usr/bin/env python
  import signal, time
  
  def handler(signum, time):
      print("\nI got a SIGINT, but I am not stopping")
  
  signal.signal(signal.SIGINT, handler)
  i = 0
  while True:
      time.sleep(.1)
      print("\r{}".format(i), end="")
      i += 1
  ```

- `SIGTERM` 则是一个更加通用的、也更加优雅地退出信号。为了发出这个信号我们需要使用 [`kill`](https://www.man7.org/linux/man-pages/man1/kill.1.html) 命令, 它的语法是： `kill -TERM <PID>`。

- `SIGSTOP` 会让进程暂停。 `Ctrl-Z` 

- 可以使用 [`fg`](https://www.man7.org/linux/man-pages/man1/fg.1p.html) 或 [`bg`](http://man7.org/linux/man-pages/man1/bg.1p.html) 命令恢复暂停的工作。它们分别表示在前台继续或在后台继续。

- [`jobs`](http://man7.org/linux/man-pages/man1/jobs.1p.html) 命令会列出当前终端会话中尚未完成的全部任务。

  可以使用 pid 引用这些任务（也可以用 [`pgrep`](https://www.man7.org/linux/man-pages/man1/pgrep.1.html) 找出 pid）。

  可以使用百分号 + 任务编号（`jobs` 会打印任务编号）来选取该任务。如果要选择最近的一个任务，可以使用 `$!` 这一特殊参数。

- 让已经在运行的进程转到后台运行，可以键入`Ctrl-Z` ，然后紧接着再输入`bg`。

  注意，后台的进程仍然是您的终端进程的子进程，一旦您关闭终端（会发送另外一个信号`SIGHUP`），这些后台的进程也会终止。

  为了防止这种情况发生，可以使用 [`nohup`](https://www.man7.org/linux/man-pages/man1/nohup.1.html) (一个用来忽略 `SIGHUP` 的封装) 来运行程序。针对已经运行的程序，可以使用`disown` 。

- `SIGKILL` 是一个特殊的信号，它不能被进程捕获并且它会马上结束该进程。不过这样做会有一些副作用，例如留下孤儿进程。

  

### Terminal multiplexers

- 像 [`tmux`](https://www.man7.org/linux/man-pages/man1/tmux.1.html) 这类的终端多路复用器可以允许我们基于面板和标签分割出多个终端窗口，这样您便可以同时与多个 shell 会话进行交互。

- 终端多路复用使我们可以分离当前终端会话并在将来重新连接。

- **tmux中对象的继承结构如下**:

  Sessions：每个会话都是一个独立的工作区，其中包含一个或多个窗口

  ​	`tmux` 开始一个新的会话

  ​	`tmux new -s NAME` 以指定名称开始一个新的会话

  ​	`tmux ls` 列出当前所有会话

  ​	在 `tmux` 中输入 `<C-b> d` ，将当前会话分离

  ​	`tmux a` 重新连接最后一个会话。您也可以通过 `-t` 来指定具体的会话

  - Windows（like tabs in browser）：相当于编辑器或是浏览器中的标签页，从视觉上将一个会话分割为多个部分

    ​	`<C-b> c` 创建一个新的窗口，使用 `<C-d>`关闭

    ​	`<C-b> N` 跳转到第 *N* 个窗口，注意每个窗口都是有编号的

    ​	`<C-b> p` 切换到前一个窗口

    ​	`<C-b> n` 切换到下一个窗口

    ​	`<C-b> ,` 重命名当前窗口

    ​	`<C-b> w` 列出当前所有窗口

    - Panes：像 vim 中的分屏一样，面板使我们可以在一个屏幕里显示多个 shell

      ​	`<C-b> "` 水平分割

      ​	`<C-b> %` 垂直分割

      ​	`<C-b> <方向>` 切换到指定方向的面板，<方向> 指的是键盘上的方向键

      ​	`<C-b> z` 切换当前面板的缩放

      ​	`<C-b> [` 开始往回卷动屏幕。您可以按下空格键来开始选择，回车键复制选中的部分

      ​	`<C-b> <空格>` 在不同的面板排布间切换

- 扩展阅读： [这里](https://www.hamvocke.com/blog/a-quick-and-easy-guide-to-tmux/) 是一份 `tmux` 快速入门教程， [而这一篇](http://linuxcommand.org/lc3_adv_termmux.php) 文章则更加详细，它包含了[`screen`](https://www.man7.org/linux/man-pages/man1/screen.1.html) 命令。



### Alias

shell 的别名相当于一个长命令的缩写，shell 会自动将其替换成原本的命令。例如，bash 中的别名语法如下：

`alias alias_name="command_to_alias arg1 arg2"`

注意， `=`两边是没有空格的，因为 [`alias`](https://www.man7.org/linux/man-pages/man1/alias.1p.html) 是一个 shell 命令，它只接受一个参数。

```bash
# 创建常用命令的缩写
alias ll="ls -lh"

# 能够少输入很多
alias gs="git status"
alias gc="git commit"
alias v="vim"

# 手误打错命令也没关系
alias sl=ls

# 重新定义一些命令行的默认行为
alias mv="mv -i"           # -i prompts before overwrite
alias mkdir="mkdir -p"     # -p make parent dirs as needed
alias df="df -h"           # -h prints human readable format

# 别名可以组合使用
alias la="ls -A"
alias lla="la -l"

# 在忽略某个别名
\ls
# 或者禁用别名
unalias la

# 获取别名的定义
alias ll
# 会打印 ll='ls -lh'
```



### Dotfiles

[shell 启动脚本](https://blog.flowblok.id.au/2013-02/shell-startup-scripts.html)

- `bash` - `~/.bashrc`, `~/.bash_profile`
- `git` - `~/.gitconfig`
- `vim` - `~/.vimrc` 和 `~/.vim` 目录
- `ssh` - `~/.ssh/config`
- `tmux` - `~/.tmux.conf`

https://dotfiles.github.io/

##### 可移植性

配置文件的一个常见的痛点是它可能并不能在多种设备上生效。例如，如果您在不同设备上使用的操作系统或者 shell 是不同的，则配置文件是无法生效的。或者，有时您仅希望特定的配置只在某些设备上生效。

有一些技巧可以轻松达成这些目的。如果配置文件 if 语句，则您可以借助它针对不同的设备编写不同的配置。例如，您的 shell 可以这样做：

```bash
if [[ "$(uname)" == "Linux" ]]; then {do_something}; fi

# 使用和 shell 相关的配置时先检查当前 shell 类型
if [[ "$SHELL" == "zsh" ]]; then {do_something}; fi

# 您也可以针对特定的设备进行配置
if [[ "$(hostname)" == "myServer" ]]; then {do_something}; fi
```

如果配置文件支持 include 功能，您也可以多加利用。例如：`~/.gitconfig` 可以这样编写：

```bash
[include]
    path = ~/.gitconfig_local
```

然后我们可以在日常使用的设备上创建配置文件 `~/.gitconfig_local` 来包含与该设备相关的特定配置。您甚至应该创建一个单独的代码仓库来管理这些与设备相关的配置。

如果您希望在不同的程序之间共享某些配置，该方法也适用。例如，如果您想要在 `bash` 和 `zsh` 中同时启用一些别名，您可以把它们写在 `.aliases` 里，然后在这两个 shell 里应用：

```bash
# Test if ~/.aliases exists and source it
if [ -f ~/.aliases ]; then
    source ~/.aliases
fi
```



### Remote machine

##### 密钥生成

使用 [`ssh-keygen`](http://man7.org/linux/man-pages/man1/ssh-keygen.1.html) 命令可以生成一对密钥：

`ssh-keygen -o -a 100 -t ed25519 -f ~/.ssh/id_ed25519`

可以为密钥设置密码，防止有人持有您的私钥并使用它访问您的服务器。

可以使用 [`ssh-agent`](https://www.man7.org/linux/man-pages/man1/ssh-agent.1.html) 或 [`gpg-agent`](https://linux.die.net/man/1/gpg-agent) ，这样就不需要每次都输入该密码了。

如果您曾经配置过使用 SSH 密钥推送到 GitHub，那么可能您已经完成了[这里](https://help.github.com/articles/connecting-to-github-with-ssh/) 介绍的这些步骤，并且已经有了一个可用的密钥对。要检查您是否持有密码并验证它，您可以运行 `ssh-keygen -y -f /path/to/key`.

##### 基于密钥的认证机制

`ssh` 会查询 `.ssh/authorized_keys` 来确认那些用户可以被允许登录。您可以通过下面的命令将一个公钥拷贝到这里：

```bash
cat .ssh/id_ed25519.pub | ssh foobar@remote 'cat >> ~/.ssh/authorized_keys'
```

如果支持 `ssh-copy-id` 的话，可以使用下面这种更简单的解决方案：

```bash
ssh-copy-id -i .ssh/id_ed25519.pub foobar@remote
```

##### 通过 SSH 复制文件

使用 ssh 复制文件有很多方法：

- `ssh+tee`, 最简单的方法是执行 `ssh` 命令，然后通过这样的方法利用标准输入实现 `cat localfile | ssh remote_server tee serverfile`。回忆一下，[`tee`](https://www.man7.org/linux/man-pages/man1/tee.1.html) 命令会将标准输出写入到一个文件；
- [`scp`](https://www.man7.org/linux/man-pages/man1/scp.1.html) ：当需要拷贝大量的文件或目录时，使用`scp` 命令则更加方便，因为它可以方便的遍历相关路径。语法如下：`scp path/to/local_file remote_host:path/to/remote_file`；
- [`rsync`](https://www.man7.org/linux/man-pages/man1/rsync.1.html) 对 `scp` 进行了改进，它可以检测本地和远端的文件以防止重复拷贝。它还可以提供一些诸如符号连接、权限管理等精心打磨的功能。甚至还可以基于 `--partial`标记实现断点续传。`rsync` 的语法和`scp`类似；

##### 端口转发

很多情况下我们都会遇到软件需要监听特定设备的端口。如果是在您的本机，可以使用 `localhost:PORT` 或 `127.0.0.1:PORT`。但是如果需要监听远程服务器的端口该如何操作呢？这种情况下远端的端口并不会直接通过网络暴露给您。

此时就需要进行 *端口转发*。端口转发有两种，（参见下图，该图片引用自这篇[StackOverflow 文章](https://unix.stackexchange.com/questions/115897/whats-ssh-port-forwarding-and-whats-the-difference-between-ssh-local-and-remot)）中的图片。

**本地端口转发**

<img src="G:\GeneralData\Pictures\typoraphoto\mit-missing-semester-summary\image-20211003175234747.png" alt="Local Port Forwarding" style="zoom:67%;" />

**远程端口转发**

<img src="G:\GeneralData\Pictures\typoraphoto\mit-missing-semester-summary\image-20211003175206989.png" alt="Remote Port Forwarding" style="zoom:67%;" />

常见的情景是使用本地端口转发，即远端设备上的服务监听一个端口，而您希望在本地设备上的一个端口建立连接并转发到远程端口上。例如，我们在远端服务器上运行 Jupyter notebook 并监听 `8888` 端口。 然后，建立从本地端口 `9999` 的转发，使用 `ssh -L 9999:localhost:8888 foobar@remote_server` 。这样只需要访问本地的 `localhost:9999` 即可。

##### SSH 配置

使用别名

```bash
alias my_server="ssh -i ~/.id_ed25519 --port 2222 -L 9999:localhost:8888 foobar@remote_server
```

使用 `~/.ssh/config`.

```bash
Host vm
    User foobar
    HostName 172.16.174.141
    Port 2222
    IdentityFile ~/.ssh/id_ed25519
    LocalForward 9999 localhost:8888

# 在配置文件中也可以使用通配符
Host *.mit.edu
    User foobaz
```

这么做的好处是，使用 `~/.ssh/config` 文件来创建别名，类似 `scp`、`rsync`和`mosh`的这些命令都可以读取这个配置并将设置转换为对应的命令行选项。

服务器侧的配置通常放在 `/etc/ssh/sshd_config`。可以在这里配置免密认证、修改 ssh 端口、开启 X11 转发等等。 也可以为每个用户单独指定配置。

##### 杂项

- 连接远程服务器的一个常见痛点是遇到由关机、休眠或网络环境变化导致的掉线。如果连接的延迟很高也很让人讨厌。[Mosh](https://mosh.org/)（即 mobile shell ）对 ssh 进行了改进，它允许连接漫游、间歇连接及智能本地回显。

- 有时将一个远端文件夹挂载到本地会比较方便， [sshfs](https://github.com/libfuse/sshfs) 可以将远端服务器上的一个文件夹挂载到本地，然后您就可以使用本地的编辑器了。
- 有很多终端模拟器可供选择（这里有一些关于它们之间[比较](https://anarc.at/blog/2018-04-12-terminal-emulators-1/)的信息）



## Git

### Git的数据模型

#### 快照

Git 将顶级目录中的文件和文件夹作为集合，并通过一系列快照来管理其历史记录。在Git的术语里，文件被称作Blob对象（数据对象），也就是一组数据。目录则被称之为“树”，它将名字与 Blob 对象或树对象进行映射（使得目录中可以包含其他目录）。快照则是被追踪的最顶层的树。例如，一个树看起来可能是这样的：

```
<root> (tree)
|
+- foo (tree)
|  |
|  + bar.txt (blob, contents = "hello world")
|
+- baz.txt (blob, contents = "git is wonderful")
```

这个顶层的树包含了两个元素，一个名为 “foo” 的树（它本身包含了一个blob对象 “bar.txt”），以及一个 blob 对象 “baz.txt”。

#### 历史记录建模：关联快照

在 Git 中，历史记录是一个由快照组成的有向无环图。

注意，快照具有多个“父辈”而非一个，因为某个快照可能由多个父辈而来。

在 Git 中，这些快照被称为“提交”。

```
o <-- o <-- o <-- o <---- o
            ^            /
             \          v
              --- o <-- o
```

Git 中的提交是不可改变的。但这并不代表错误不能被修改，只不过这种“修改”实际上是创建了一个全新的提交记录。而引用则被更新为指向这些新的提交。

#### 数据模型及其伪代码表示

```
// 文件就是一组数据
type blob = array<byte>

// 一个包含文件和目录的目录
type tree = map<string, tree | blob>

// 每个提交都包含一个父辈，元数据和顶层树
type commit = struct {
    parent: array<commit>
    author: string
    message: string
    snapshot: tree
}
```

#### 对象和内存寻址

Git 中的对象可以是 blob、树或提交：

```
type object = blob | tree | commit
```

Git 在储存数据时，所有的对象都会基于它们的 [SHA-1 哈希](https://en.wikipedia.org/wiki/SHA-1) 进行寻址。

```
objects = map<string, object>

def store(object):
    id = sha1(object)
    objects[id] = object

def load(id):
    return objects[id]
```

Blobs、树和提交都一样，它们都是对象。当它们引用其他对象时，它们并没有真正的在硬盘上保存这些对象，而是仅仅保存了它们的哈希值作为引用。

对象可以通过 `git cat-file -p HASH-VALUE` 来进行可视化

#### 引用

Git 给这些哈希值赋予人类可读的名字，也就是引用（references）。引用是指向提交的指针。与对象不同的是，它是可变的（引用可以被更新，指向新的提交）。例如，`master` 引用通常会指向主分支的最新一次提交。

```
references = map<string, string>

def update_reference(name, id):
    references[name] = id

def read_reference(name):
    return references[name]

def load_reference(name_or_id):
    if name_or_id in references:
        return load(references[name_or_id])
    else:
        return load(name_or_id)
```

这样，Git 就可以使用诸如 “master” 这样人类可读的名称来表示历史记录中某个特定的提交，而不需要在使用一长串十六进制字符了。

在 Git 中，我们当前的位置有一个特殊的索引，它就是 “HEAD”。

#### 仓库

 Git 仓库的定义：`对象` 和 `引用`。

在硬盘上，Git 仅存储对象和引用：因为其数据模型仅包含这些东西。所有的 `git` 命令都对应着对提交树的操作，例如增加对象，增加或删除引用。

#### 暂存区

“暂存区（staging area）”的机制允许您指定下次快照中要包括那些改动。

使用场景：

- 您开发了两个独立的特性，然后您希望创建两个独立的提交，其中第一个提交仅包含第一个特性，而第二个提交仅包含第二个特性。
- 假设您在调试代码时添加了很多打印语句，然后您仅仅希望提交和修复 bug 相关的代码而丢弃所有的打印语句。

#### Git的命令行接口

阅读 [Pro Git 中文版](https://git-scm.com/book/zh/v2)

- `git help <command>`: 获取 git 命令的帮助信息
-  `git commit`: 创建一个新的提交
  - 如何编写 [良好的提交信息](https://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html)!
  - 为何要 [编写良好的提交信息](https://chris.beams.io/posts/git-commit/)
- `git log --all --graph --decorate`: 可视化历史记录（有向无环图）
- `git diff <filename>`: 显示与暂存区文件的差异
- `git diff <revision> <filename>`: 显示某个文件两个版本之间的差异
- `git checkout <revision>`: 更新 HEAD 和目前的分支
- `git checkout -b <name>`: 创建分支并切换到该分支
  - 相当于 `git branch <name>; git checkout <name>`
- `git mergetool`: 使用工具来处理合并冲突
- `git rebase`: 将一系列补丁变基（rebase）为新的基线
- `git remote add <name> <url>`: 添加一个远端
- `git push <remote> <local branch>:<remote branch>`: 将对象传送至远端并更新远端引用
- `git branch --set-upstream-to=<remote>/<remote branch>`: 创建本地和远端分支的关联关系
- `git commit --amend`: 编辑提交的内容或信息
- `git reset HEAD <file>`: 恢复暂存的文件
- `git checkout -- <file>`: 丢弃修改

#### Git高级操作

- `git config`: Git 是一个 [高度可定制的](https://git-scm.com/docs/git-config) 工具
- `git clone --depth=1`: 浅克隆（shallow clone），不包括完整的版本历史信息
- `git add -p`: 交互式暂存
- `git rebase -i`: 交互式变基
- `git blame`: 查看最后修改某行的人
- `git stash`: 暂时移除工作目录下的修改内容
- `git bisect`: 通过二分查找搜索历史记录
- `.gitignore`: [指定](https://git-scm.com/docs/gitignore) 故意不追踪的文件

#### 杂项

- **图形用户界面**: Git 的 [图形用户界面客户端](https://git-scm.com/downloads/guis) 有很多，但是我们自己并不使用这些图形用户界面的客户端，我们选择使用命令行接口
- **Shell 集成**: 将 Git 状态集成到您的 shell 中会非常方便。([zsh](https://github.com/olivierverdier/zsh-git-prompt), [bash](https://github.com/magicmonty/bash-git-prompt))。[Oh My Zsh](https://github.com/ohmyzsh/ohmyzsh)这样的框架中一般以及集成了这一功能
- **编辑器集成**: 和上面一条类似，将 Git 集成到编辑器中好处多多。[fugitive.vim](https://github.com/tpope/vim-fugitive) 是 Vim 中集成 GIt 的常用插件
- **工作流**: 我们已经讲解了数据模型与一些基础命令，但还没讨论到进行大型项目时的一些惯例 ( 有[很多](https://nvie.com/posts/a-successful-git-branching-model/) [不同的](https://www.endoflineblog.com/gitflow-considered-harmful) [处理方法](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow))
- **GitHub**: Git 并不等同于 GitHub。 在 GitHub 中您需要使用一个被称作[拉取请求（pull request）](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-requests)的方法来向其他项目贡献代码
- **其他 Git 提供商**: GitHub 并不是唯一的。还有像 [GitLab](https://about.gitlab.com/) 和 [BitBucket](https://bitbucket.org/) 这样的平台。

#### 资源

- [Pro Git](https://git-scm.com/book/en/v2) ，**强烈推荐**！学习前五章的内容可以教会您流畅使用 Git 的绝大多数技巧，因为您已经理解了 Git 的数据模型。后面的章节提供了很多有趣的高级主题。（[Pro Git 中文版](https://git-scm.com/book/zh/v2)）；
- [Oh Shit, Git!?!](https://ohshitgit.com/) ，简短的介绍了如何从 Git 错误中恢复；
- [Git for Computer Scientists](https://eagain.net/articles/git-for-computer-scientists/) ，简短的介绍了 Git 的数据模型，与本文相比包含较少量的伪代码以及大量的精美图片；
- [Git from the Bottom Up](https://jwiegley.github.io/git-from-the-bottom-up/)详细的介绍了 Git 的实现细节，而不仅仅局限于数据模型。好奇的同学可以看看；
- [How to explain git in simple words](https://smusamashah.github.io/blog/2017/10/14/explain-git-in-simple-words)；
- [Learn Git Branching](https://learngitbranching.js.org/) 通过基于浏览器的游戏来学习 Git ；

## References

- [The Missing Semester of Your CS Education](https://missing.csail.mit.edu/)
- [B站视频](https://www.bilibili.com/video/BV1x7411H7wa)

