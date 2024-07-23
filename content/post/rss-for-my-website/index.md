+++
title = '用 RSS 订阅本站的所有文章或按标签、分类订阅文章'
slug = 'rss-for-my-website'
date = 2024-07-23T09:57:30+08:00
description = '介绍用 RSS 订阅本站的所有文章或按标签、分类订阅文章，并讲解原理'
keywords = ['RSS']
tags = ['RSS']
categories = ['Skill']
+++

本站所用的主题没有直接把 RSS 链接显示在网页上，而是放在了 HTML `<link>` 标签里，但是要怎么订阅本站的所有或部分文章呢？

## HowTo
如果要订阅本站所有文章，在 RSS 阅读器里直接订阅 `https://mumulhl.eu.org`。如果要根据标签、分类订阅，直接订阅相应的链接就可以了，如 `https://mumulhl.eu.org/categories/skill/`。

（RSS 还能这样订阅以前都不知道，最近才意外知道）

# 如何实现？
HTML `<link>` 最常见的用法就是为网页引入 CSS 文件，它的作用是标志网页和外部资源的关系。

```html
<link href="main.css" rel="stylesheet">
```

`href` 属性的值是文件路径，`rel` 则是 _relationship_ 的缩写，表示网页和外部资源的关系。`rel` 是 `stylesheet`，就表示 main.css 是该网页的 CSS。

引入 RSS 的 `<link>` 是长这样的：

```html
<link href="index.xml" rel="alternate" type="application/rss+xml">
```

`type` 的值为 `application/rss+xml` 标志了 index.xml 的类型是 RSS，`rel` 的值为 `alternate` 根据 [MDN](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/rel) 意思是当前网页的替代。

RSS 阅读器通常支持这种方式订阅，以前我只知道输入 RSS 文件的链接订阅...
