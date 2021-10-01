---
title: Learn Git Branching Note
date: 2021-10-01 15:17:26
categories: Tool
tags: Note
description: Learn Git Branching Note
keywords:
cover: false
---





# [Learn Git Branching](https://learngitbranching.js.org/) Note

> "Learn Git Branching" is the most visual and interactive way to learn Git on the web; you'll be challenged with exciting levels, given step-by-step demonstrations of powerful features, and maybe even have a bit of fun along the way.

`show commands` 列出所有可用指令

## Level Main

### Introduction Sequence 基础篇

Git 仓库中的**提交记录**保存的是你的目录下所有文件的快照。Git 希望提交记录尽可能地轻量，因此在你每次进行提交时，它并不会盲目地复制整个目录。条件允许的情况下，它会将当前版本与仓库中的上一个版本进行对比，并把所有的差异打包到一起作为一个提交记录。

Git 的分支也非常轻量。它们只是简单地指向某个提交纪录。

`git merge `在合并两个分支时会产生一个特殊的提交记录，它有两个父节点。

`git rebase`合并分支时，会取出一系列的提交记录，“复制”它们，然后在另外一个地方逐个的放下去。Rebase 的优势就是可以创造更线性的提交历史。原来的提交记录依然存在。

```bash
# 提交
git commit -m "commit message"

# 创建新分支
git branch newBranch

# 切换到分支
git checkout someBranch

# 创建并切换到新分支
git checkout -b newBranch

# 把某分支合并到当前分支
git merge someBranch

# 把当前分支合并到某分支去
git rebase someBranch
git rebase toCommit fromCommit
```



### Ramping Up 高级篇

#### 移动提交记录

HEAD 是一个对当前检出的提交记录的符号引用，在通常情况下是指向分支名的。

在提交的时候，改变的是分支的状态，即分支所指向的提交记录。

如果想看 HEAD 指向，可以通过 `cat .git/HEAD` 查看， 如果 HEAD 指向的是一个引用，还可以用 `git symbolic-ref HEAD` 查看它的指向。

<img src="http://img.luooofan.site/20211001-151413-image-20211001011509483.png" alt="分离的HEAD" style="zoom:67%;" />

通过指定提交记录哈希值的方式在 Git 提交树中移动不太方便。Git 引入了相对引用。

- 使用 `^` 向上移动 1 个提交记录，可连用
- 使用 `~<num>` 向上移动多个提交记录，如 `~3`

我（作者）使用相对引用最多的就是移动分支。可以直接使用 `-f` 选项让分支指向另一个提交。

个人理解：HEAD和分支最终都会指向提交记录，Git的很多命令都是针对提交记录的

```bash
# 移动HEAD到某个提交记录 如果原来HEAD指向分支名，那么现在是分离的HEAD
git checkout C1

# 移动HEAD到main的父节点（父提交记录）
git checkout main^

# 移动main分支强制指向到HEAD的第3级父提交
git branch -f main HEAD~3
```

#### 撤销变更

主要有两种方法用来撤销变更，一是 `git reset`，还有就是 `git revert`，后面跟提交记录。

`git reset` 通过把分支记录回退几个提交记录来实现撤销改动。它向上移动分支，原来指向的提交记录就跟从来没有提交过一样。

在 reset 后，所做的变更还在，但是处于未加入暂存区状态。（取决于 reset 命令的参数设置）

reset 对远程分支无效，为了撤销更改并分享给别人，我们需要使用 `git revert`。

revert 在要撤销的提交记录后面多做一个新提交。新提交记录所做的更改用来撤销原提交。





### Moving Work Around 整理提交记录

- `git cherry-pick <提交号>...` 如果你想将一些提交复制到当前所在的位置（`HEAD`）下面的话， Cherry-pick 是最直接的方式了。

- 交互式的 rebase ：如果你不清楚你想要的提交记录的哈希值

  它指的是使用带参数 `--interactive` 的 rebase 命令, 简写为 `-i`

  Git 会打开一个 UI 界面并列出将要被复制到目标分支的备选提交记录，它还会显示每个提交记录的哈希值和提交说明，提交说明有助于你理解这个提交进行了哪些更改。

```bash
# 在HEAD下重做C2 C4提交
git cherry-pick C2 C4
```



### A Mixed Bag 杂项

#### 提交的技巧

这种情况是很常见的：你之前在 `newImage` 分支上进行了一次提交，然后又基于它创建了 `caption` 分支，然后又提交了一次。此时你想对某个以前的提交记录进行一些小小的调整。

可以通过下面的方法来克服困难：

- 先用 `git rebase -i` 将提交重新排序，然后把我们想要修改的提交记录挪到最前
- 然后用 `git commit --amend` 来进行一些小修改
- 接着再用 `git rebase -i` 来将他们调回原来的顺序
- 最后我们把 main 移到修改的最前端

**问题**：要进行两次排序，而这有可能造成由 rebase 而导致的冲突。

![image-20211001020452724](http://img.luooofan.site/20211001-151414-image-20211001020452724.png)

cherry-pick 可以将提交树上任何地方的提交记录取过来追加到 HEAD 上（只要不是 HEAD 上游的提交就没问题）。

![image-20211001021035341](http://img.luooofan.site/20211001-151415-image-20211001021035341.png)



#### Git Tags

分支很容易被人为移动，并且当有新的提交时，它也会移动。分支很容易被改变，大部分分支还只是临时的，并且还一直在变。

Git 的 tag 可以在某种程度上（因为标签可以被删除后重新在另外一个位置创建同名的标签）永久地将某个特定的提交命名为里程碑，然后就可以像分支一样引用了。

**用法**：`git tag TagName someCommit` 给某个提交记录打上tag



#### Git Describe

由于标签在代码库中起着**锚点**的作用， `git describe`用来描述离你最近的锚点（标签）。

它能帮你在提交历史中移动了多次以后找到方向；当你用 `git bisect`（一个查找产生 Bug 的提交记录的指令）找到某个提交记录时，或者是当你坐在你那刚刚度假回来的同事的电脑前时， 可能会用到这个命令。

**语法**：`git describe <ref>`

`<ref>` 可以是任何能被 Git 识别成提交记录的引用，如果你没有指定的话，Git 会用你目前所检出的位置（`HEAD`）。

它**输出**的结果是这样的：`<tag>_<numCommits>_g<hash>`

`tag` 表示的是离 `ref` 最近的标签， `numCommits` 是表示这个 `ref` 与 `tag` 相差有多少个提交记录， `hash` 表示的是你所给定的 `ref` 所表示的提交记录哈希值的前几位。

当 `ref` 提交记录上有某个标签时，则只输出标签名称

![image-20211001022442626](http://img.luooofan.site/20211001-151416-image-20211001022442626.png)



### Advanced Topics 高级话题

#### 选择父提交记录

操作符 `^` 后面也可以跟一个数字，指定合并提交记录的某个父提交。

Git 默认选择合并提交的“第一个”父提交，在操作符 `^` 后跟一个数字可以改变这一默认行为。

操作符 `^` 和 `~` 可以链式使用



## Level Remote

### Push & Pull -- Git Remotes!

远程分支反映了远程仓库(在你上次和它通信时)的**状态**。

远程分支有一个特别的属性，在你检出时自动进入分离 HEAD 状态。Git 这么做是出于不能直接在这些分支上进行操作的原因。

远程分支有一个命名规范`<remote name>/<branch name>`

![image-20211001140416158](http://img.luooofan.site/20211001-151417-image-20211001140416158.png)



`git fetch` 实际上将本地仓库中的远程分支更新成了远程仓库相应分支最新的状态。它并不会改变你本地仓库的状态。它不会更新你的 `main` 分支，也不会修改你磁盘上的文件。

- 从远程仓库下载本地仓库中缺失的提交记录
- 更新远程分支指针(如 `o/main`)

`git pull` 就是 git fetch 和 git merge 的缩写。

`git push` 负责将变更上传到指定的远程仓库，并在远程仓库上合并你的新提交记录。该命令不带任何参数时的行为与 Git 的一个名为 `push.default` 的配置有关。它的默认值取决于你正使用的 Git 的版本，在教程中使用的是 `upstream`。

`git pull --rebase` 就是 fetch 和 rebase 的简写。



### To Origin And Beyond -- Advanced Git Remotes!

#### 设置远程追踪分支

可以指定隐含的 push 目的地以及 merge 的目标。

- 通过远程分支检出一个新的分支，执行:`git checkout -b totallyNotMain o/main`
  就可以创建一个名为 `totallyNotMain` 的分支，它跟踪远程分支 `o/main`。
- `git branch -u` 命令，执行：`git branch -u o/main foo`
  这样 `foo` 就会跟踪 `o/main` 了。



#### Git Push/Fetch/Pull 的参数

`git push origin main`：切到本地仓库中的“main”分支，获取所有的提交，再到远程仓库“origin”中找到“main”分支，将远程仓库中没有的提交记录都添加上去。

同时指定了提交记录的来源和去向：`git push <remote> <place>`

同时为源和目的指定\<place\>：`git push origin <source>:<destination>`

如果要推送到的目的分支不存在的话，会在远程仓库中根据你提供的名称帮你创建这个新分支。



`git fetch origin foo`：Git 会到远程仓库的 `foo` 分支上，然后获取所有本地不存在的提交，放到本地的 `o/foo` 上。

Fetch指定 `<source>:<destination>` 的话，`source` 现在指的是远程仓库中的位置，而 `<destination>` 才是要放置提交的本地仓库的位置。它与 git push 刚好相反，



可以在 git push 或 git fetch 时不指定任何 `source`，方法就是仅保留冒号和 destination 部分，source 部分留空。

- `git push origin :side`：删除远程side分支
- `git fetch origin :bugFix`：会在本地创建一个新分支bugFix



`git pull origin foo` 相当于：`git fetch origin foo; git merge o/foo`

`git pull origin bar~1:bugFix` 相当于：`git fetch origin bar~1:bugFix; git merge bugFix`

git pull 实际上就是 fetch + merge 的缩写, git pull 唯一关注的是提交最终合并到哪里（也就是为 git fetch 所提供的 destination 参数）

![image-20211001150824428](http://img.luooofan.site/20211001-151417-image-20211001150824428.png)

