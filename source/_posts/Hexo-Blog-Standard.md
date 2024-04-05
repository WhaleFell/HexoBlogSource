---
title: 博客写作规范约束
date: 2018-12-11 15:51:48
updated: 2023-12-11 15:59:48
categories:
  - Hexo
tags:
  - Hexo
  - Blog
description: 编写博客时的规范约束,优化 SEO,提高检索效率. 
date updated: 2023-06-29 23:18
---

# 博客写作规范约束

## 头注

> reference: [Fluid 主题文档](https://fluid-dev.github.io/hexo-fluid-docs/guide/)

1. 头注必须注明 `title` 、`data` 、`categories`(分类)、`tags`(标签)，其中分类和标签需要统一使用英文字符，`post_/` 目录下的 `Markdown` 文件名需要使用英文，`hexo` 会根据文件名生成路径。
2. md 文件名遵从 `Key-Word.md` 格式，单词之间用 `-` 分隔，每个单词需要首字母大写。
3. `categories` 有且只有一个,`tags` 可以有多个.

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

严格遵循 `markdown` 语法,不使用任何不是标准 MD 兼容的语法,避免迁移时出现问题.

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
