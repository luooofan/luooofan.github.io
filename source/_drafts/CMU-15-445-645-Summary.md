---
title: CMU 15-445 645 Summary
tags:
---

建议按照官方配置来走，用clang-tidy-8不要用更高版本

本地的测试用例只有一部分，网站上的测例才是全部

做不同年份的projects需要根据commit time和commit message回退到当时的repo

win上用户可以配置wsl环境，ubuntu 20.04可能会在安装clang的时候发现依赖冲突（无法确定是感觉有可能是装cuda11导致的）

vscode cpptools对模板类的intelli sense做的还不好

模板类的派生类内不能直接使用父类的成员变量，需要加限定

unique ptr需要move

建议在不同的分支下做project，比如p0-2020，p1-2020，...，p0-2021等等

取消测例的DISABLE要修改对应的测试文件（cpp）

## Before Submit

每次自行测试后（只验证了本地测例的正确性）应该：

```bash
cd build
make format
make check-lint
make check-clang-tidy
```

创建zip压缩包，以P0为例：`zip -r p0.zip ./src/include/primer/p0_starter.h`

可以用`-u`更新包内单个文件

查看压缩包结构：`unzip -l p0.zip`

打开win下的文件管理器：`explorer.exe .`

然后拖拽压缩包上传提交即可

