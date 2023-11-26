---
title: Hexo 博客写作规范约定
date: 2018-12-11T15:51:48.000Z
updated: 2018-12-11T15:59:48.000Z
categories: Hexo
tags:
  - Hexo
  - Blog
description: 在编写本 Hexo 博客时需要遵循的既定格式，方便规范和索引。
date updated: 2023-06-29 23:18
---

# Hexo 博客写作规范约定

于 2023/4/11 因为原域名 [lskyl.xyz](https://lskyl.xyz) 到期了,故把博客域名和 oss 域名整体迁移到 [whaleluo.top](https://whaleluo.top) 取鲸（Whale 英）落（Luo 中）之意.其中带 www 前缀 [www.whaleluo.top](https://www.whaleluo.top) 经过 cloudflare 加速，用于某些不能直接访问到 github pages 的场景。同时搭建在 vercel `/vɜː'sel/` 上的 [https:/blog.whaleluo.top)](https:/blog.whaleluo.top) 作为备份。

于 2023/2/10 更换了更快的博客主题 **Card** [Card](https://github.com/ChrAlpha/hexo-theme-cards).[主题文档](https://theme-cards.ichr.me/)

于 2022/12/11 重新拾起荒废已久的 Hexo 博客，就当是给自己的笔记、想法一个家叭。还是得规范一下博客写作的格式方便自己收纳的。

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

## 插入图片并储存在 七牛云 OSS 上

## 本地部署

```shell
hexo generate --watch --deploy
```
