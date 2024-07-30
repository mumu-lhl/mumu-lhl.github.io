+++
title = '简易友链朋友圈搭建教程'
slug = 'simple-friend-circle-tutorial'
date = 2024-07-30T11:41:16+08:00
description = '简易友链朋友圈搭建教程'
keywords = ['教程', '友链', '友链朋友圈', '搭建']
tags = ['教程']
categories = ['Tech']
+++

[Simple Friend Circle](https://github.com/Simple-Friend-Circle/simple-friend-circle) 是我偶然想到的一个超简单的友链朋友圈，它不需要后端，只需要 CI 去定时拉取博文，然后生成静态页面，再在自己的博客上用 `<iframe>` 引入这个静态页面。

## 搭建

Fork 这个项目，在 Actions 中开启 workflows，然后开启叫作 Friend Circle 的 workflow，关闭叫作 Lint Commit Messages 的 workflow，之后在 Settings 的 Pages 中将 Branch 选为 gh-pages。

（是不是很简单？:)）

## 配置

links 文件用于配置各个博客的 RSS 链接和头像，像这样：

```
https://mumulhl.eu.org/index.xml https://mumulhl.eu.org/img/avatar_hub440208ea63c4061633255bf6046ed7b_104338_300x0_resize_q75_h2_box_2.webp
```

## 插入博客

在想要插入友链朋友圈的网页加入这段 HTML：

```html
<iframe src="https://YOUR GITHUB NAME.github.io/simple-friend-circle/" width="100%" height="600rem" style="border:none;"></iframe>
```

## 样式

像颜色之类的可以到 public/main.css 自行调整，默认的颜色来自我用的 [Stack](https://stack.jimmycai.com/) 主题。
