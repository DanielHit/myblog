---
title: Hexo 使用方法
date: 2016-06-14 14:12:09
tags: hexo
---

### 1. 生成文章
`hexo new [文章名称]` 在`_post`下面生成相应的文章
`hexo new page "目录名称"` 生成了新的Menu

<!-- more -->

### 2. 文章生成
打开 `/source/_posts` 文件夹，打开生成新的文章，开始编辑. 可以使用sublime进行文章编辑

### 3. 生成静态网页
将markdown文件生成html的文件，`hexo generate` 一键生成


### 4 部署到服务器
在配置文件部署了服务器之后，可以使用 hexo 的一键部署功能, `hexo deploy` 便可以部署到相应的git 仓库

之后，便可以登录你的域名查看新生成的文章了!

----
1. 具体的hexo安装和使用说明 https://help.github.com/articles/what-are-github-pages/
2. github 个人网站托管说明 https://hexo.io/