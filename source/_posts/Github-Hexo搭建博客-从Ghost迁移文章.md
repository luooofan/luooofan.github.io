---
title: Github+Hexo搭建博客|从Ghost迁移文章
categories: 记录
tags:
  - Blog
  - 记录
description: 利用Github repo的Pages搭建静态博客，使用Hexo框架，并迁移原Ghost博客中的全markdown格式的文章
cover: false
date: 2021-09-13 09:52:23
keywords:
---





# Github+Hexo搭建博客|从Ghost迁移文章

## 搭建博客

### 搭建步骤

1. 本地找一个文件夹，以后博客相关的文件都放在里面就行
2. 安装[Hexo](https://hexo.io/)，熟悉常用命令
3. 修改配置文件`_config.yml`
4. GitHub配置SSH Key，创建个人仓库：username.github.io（仓库名）
5. 修改`deploy`配置将Hexo与Github关联起来
6. 在Github仓库设置的Pages里自定义域名，去域名服务商控制台添加解析记录
7. 更换主题，修改配置

### 好看主题

- [hexo-theme-ayer](https://github.com/shen-yu/hexo-theme-ayer)
- [hexo-theme-matery](https://github.com/blinkfox/hexo-theme-matery)
- [hexo-theme-next](https://github.com/theme-next/hexo-theme-next)
- [hexo-theme-butterfly](https://github.com/jerryc127/hexo-theme-butterfly)

### Tips

- 如果打算使用图床、配置CDN的话需要用HTTPS协议，如果使用HTTP的话会无法访问图片（受限于浏览器，考虑到安全性）
- 部署可以配置使用ssh协议，需要配置ssh key，这样在`hexo d`的时候可以不用输用户名和密码
- 可以多端部署，域名解析时可以分国内外流量
- 在该Github repo下可以新设一个分支来用Pages，再设一个分支用来备份博客



## 从GHOST迁移

首先要从ghost后台管理页面里 Labs➡Migration Options➡Export your content 导出全部文章和设置信息，是一份json文件。

这份json文件其实就已经包括了我们所需要的全部信息了，可以直接写脚本提取文章，不过在此之前我们可以先看看它到底包括了哪些信息。

- 连接mysql数据库：`mysql -uroot -p`

- 切换到ghost database并查看有哪些表：`use ghost; show tables;`

  <img src="http://img.luooofan.site/20210918-180128-image-20210918165046323.png" alt="image-20210918165046323" style="zoom: 67%;" />

- 其中文章信息就在**posts**表中，查看其表项：`desc posts;`

  <img src="http://img.luooofan.site/20210918-180134-image-20210918165341540.png" alt="image-20210918165341540" style="zoom:67%;" />
  
- 再来看导出的这份json文件，其实就是把ghost数据库里的内容都导出来了

  <img src="https://img.luooofan.site/20210918-180139-image-20210918173007981.png" alt="image-20210918173007981" style="zoom:67%;" />

- posts表中的mobiledoc字段保留了原始的markdown文本

- 提取代码如下：

  **NOTE：脚本只提取Markdown Card，其他类型的Card需要自行实现**


```python
import json
f = open("./luooofans-blog.ghost.2021-08-29-13-36-39.json", 'r')
json_obj = json.load(f) # dict: {'db': db_obj(list)}

# get ghost database
tables = json_obj['db'][0]['data'] # dict(key:table_name(str), value:table_data(list))
# get table: posts
posts = tables['posts']
# post num: len(posts)

# key columns for me: title, mobiledoc(include markdown-style text), published_at
for post in posts:
    title = post["title"]
    published_at = post["published_at"]
    mobiledoc = post["mobiledoc"] # str
    cards = json.loads(mobiledoc)["cards"] # list
    # only process markdown-type card
    mdobj = json.loads(mobiledoc)['cards'] 
    with open('./ghost_md_files/'+title+'.md', 'w') as output:
        output.write("---\n")
        output.write("title: "+title+'\n')
        output.write("date: "+published_at[0:10]+' '+published_at[11:-5]+'\n')
        output.write("categories: "+'\n')
        output.write("tags: "+'\n')
        output.write("description: "+'\n')
        output.write("keywords: "+'\n')
        output.write("---\n")
        output.write("# "+title+'\n')
        for card in cards:
            if card[0]=="markdown":
                output.write(card[1]['markdown'])
                output.write('\n')

f.close()
```

提取出原始的md文件之后，放到hexo blog下source里的`_posts`里然后cmd里`hexo clean && hexo generate && hexo server`就行了



## References

- [GitHub+Hexo 搭建个人网站详细教程](https://zhuanlan.zhihu.com/p/26625249)
- [Hexo+Github轻松搭建个人博客](https://segmentfault.com/a/1190000038619366)
- [Hexo技巧与经验之升级(一)](http://imbajin.com/2016-10-06-Hexo%E6%8A%80%E5%B7%A7%E5%92%8C%E7%BB%8F%E9%AA%8C%E4%B8%80/)
- [Hexo之更换背景及透明度](https://blog.csdn.net/qq_43740362/article/details/113790851)
- [解决 Hexo 在使用 Node.js 14 时的 Accessing non-existent property 'xxx' of module exports inside circular dependency 问题](https://www.haoyizebo.com/posts/710984d0/)
- [为什么http的内容不能在https内使用？](https://juejin.cn/post/6873285689232850952)
- [在Github上备份Hexo博客](https://lrscy.github.io/2018/01/26/Hexo-Github-Backup/)

