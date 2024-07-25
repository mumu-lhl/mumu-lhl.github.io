+++
title = '检测 Python 代码所需的最低版本'
slug = 'detect-minimum-python-version'
date = 2024-07-22T14:56:53+08:00
description = '用 Vermin 检测 Python 代码所需的最低版本'
keywords = ['Python', '工具']
tags = ['Python', '工具']
categories = ['Tech']
+++

以后也会写像这样的工具类文章，写得特快...不会写得很啰唆，简单利落。

## Vermin
[Vermin](https://github.com/netromdk/vermin) 是一个检测 Python 代码所需的最低版本的工具，在本文写时已经有一段时间没有更新了。

## 安装

```sh
# pip
pip install vermin

# pipx
pipx install vermin

# AUR
yay -S python-vermin
# or
paru -S python-vermin
```

## 使用

```sh
# 检测目录下的所有 Python 代码，这条命令可以满足大部分需求，不需要怎样配置
vermin .
```
