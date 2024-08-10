+++
title = 'dig 的现代替代品 doggo'
slug = 'doggo-tutorial'
date = 2024-08-04T15:36:46+08:00
description = ''
keywords = ['doggo', 'DNS', '教程']
tags = ['教程']
categories = ['Skill']
+++

dig 是用来查询 DNS 的工具，[doggo](https://github.com/mr-karan/doggo) 则是它现代的增强品。

有个叫作 [dog](https://github.com/ogham/dog/) 的 dig 替代品，但是已经多年没更新了，doggo 的灵感就是来自 dog。写这篇文章时，doggo 依然保持更新。

doggo 不仅是一个命令行工具，还有个[网页版](https://doggo.mrkaran.dev)。

## 特点

* 采用彩色编码和表格格式的人类可读输出
* 支持 JSON 输出，便于脚本编写和解析
* 多种传输协议： DoH、DoT、DoQ、TCP、UDP、DNSCrypt
* 支持 `ndots` 和 `search` 配置
* 支持多个解析器，可自定义查询策略
* 支持 IPv4 和 IPv6
* 提供网络接口
* 提供 `zsh` 和 `fish` 的补全
* 反向 DNS 查询
* 灵活的查询选项，包括各种 DNS 标志
* 用于故障排除的调试模式
* 响应时间测量
* 支持跨平台

## 安装

### 脚本

```sh
curl -sS https://raw.githubusercontent.com/mr-karan/doggo/main/install.sh | sh
```

### 包管理

* Homebrew: `brew install doggo`
* Arch Linux: `yay -S doggo-bin`
* Scoop (Windows): `scoop install doggo`
* Eget: `eget mr-karan/doggo`

### 二进制文件

<https://github.com/mr-karan/doggo/releases>

### Go Install

```sh
go install github.com/mr-karan/doggo/cmd/doggo@latest
```

## 快速上手

不想打五个字的话，可以 `alias dig="doggo"`，把 dig 设置为 doggo 的别名，这样就只用打三个字了。

```sh
doggo example.com       # 查询 A 记录

doggo example.com AAAA  # 查询 AAAA 记录

doggo example.com A NS  # 查询多个记录
```

输出结果非常简洁，而且带有颜色。

## 结语

文本所介绍的内容已经可以满足大部分需求了，想要更深入使用，可以观光下文档 <https://doggo.mrkaran.dev/docs/>。
