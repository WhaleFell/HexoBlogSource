---
title: Blog 大事记与写作规范
date: 2018-12-11T15:51:48.000Z
updated: 2023-12-11T15:59:48.000Z
categories: Hexo
tags:
  - Hexo
  - Blog
description: 记录一些博客的变更，在编写 Blog 时需要遵循的既定格式，方便规范和索引。
date updated: 2023-06-29 23:18
---

# Blog 大事记与写作规范

于 2023/12/17，将博客整体迁移到了 [vercel](https://vercel.com/) 上,并写了一个 onedrive 图床程序，用于博客的静态文件存储。    
[https://whaleluo.top](https://whaleluo.top/) 博客主域名，由 vercel 提供静态托管。
[https://whalefell.github.io](https://whalefell.github.io/) 博客备用域名，由 github pages 提供静态托管。（好处是域名不会过期，~~大概我死了也可以访问~~）  
[https://api.whaleluo.top] 搭建在 vercel 的 API 服务，提供代理下载，onedrive 直连等功能。
[https://api-render.whaleluo.top] 搭建在 render.com 同上，作为备份。数据、日志互通。

~~于 2023/4/11 因为原域名 [lskyl.xyz](https://lskyl.xyz) 到期了,故把博客域名和 oss 域名整体迁移到 [whaleluo.top](https://whaleluo.top) 取鲸（Whale 英）落（Luo 中）之意.其中带 www 前缀 [www.whaleluo.top](https://www.whaleluo.top) 经过 cloudflare 加速，用于某些不能直接访问到 github pages 的场景。同时搭建在 vercel `/vɜː'sel/` 上的 [https:/blog.whaleluo.top)](https:/blog.whaleluo.top) 作为备份。~~

~~于 2023/2/10 更换了更快的博客主题 **Card** [Card](https://github.com/ChrAlpha/hexo-theme-cards).[主题文档](https://theme-cards.ichr.me/)~~

~~于 2022/12/11 重新拾起荒废已久的 Hexo 博客，就当是给自己的笔记、想法一个家叭。还是得规范一下博客写作的格式方便自己收纳的。~~

## 头注

> reference: [Fluid 主题文档](https://fluid-dev.github.io/hexo-fluid-docs/guide/)

头注必须注明 `title` 、`data` 、`categories`(分类)、`tags`(标签)，其中分类和标签需要统一使用英文字符，`post_/` 目录下的 `Markdown` 文件名需要使用英文，`hexo` 会根据文件名生成路径。

md 文件名遵从 `Key-Word.md` 格式，单词之间用 `-` 分隔，每个单词需要首字母大写。

```md
---
title: Hexo 博客写作规范约定
date: 2022-12-11 15:51:48
updated: 2022-12-11 15:59:48
categories: 
    - Hexo
tags: [Hexo, Blog]
description: Hexo 博客写作规范约定
thumbnail: http://....jpg
---
```

根据时间排序文章.

该参数可以设置置顶文章，sticky 数值越大，该文章越靠前，达到类似于置顶的效果，其他未设置的文章依然按默认排序。

```text
sticky: 100
```

## Blog Banner Img 生成

使用 [CoverView](https://coverview.vercel.app/) 设计博客 banner 图片，统一头图风格！

## MarkDown 语法

## 图床

已经部署在 OneDrive 上了，速度快，简洁，方便：

使用的是自制的 onedrive 直连提取 python 程序(base on fastapi)，并部署到 vercel 上，使用方法：

```shell
https://api.whaleluo.top/onedrive/file/?path={path}
```

vercel 感觉对 FastAPI 的支持不是很好，所以用 render.com 上也部署了一个：

```shell
https://api-render.whaleluo.top/onedrive/file/?path={path}
```


## 本地部署

```shell
hexo generate --watch --deploy
```
