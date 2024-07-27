+++
title = '存档Youtube Music ID 批量下载音乐'
slug = 'yt-music-archive-tutorial'
date = 2024-07-27T22:02:28+08:00
description = '用 yt-music-archive 存档 Youtube Music ID 批量下载音乐'
keywords = ['工具']
tags = ['工具']
categories = ['技巧']
+++

[yt-music-archive](https://github.com/mumu-lhl/yt-music-archive) 是我写的一个小脚本，用于将 Youtube Music 上的音乐下载到本地，并将其 ID 存入一个存档文件中，也可以从存档文件中还原出音乐。

（我在花了几个小时写完后，才想起用 yt-dlp 下载播放列表也可以做到同样的功能，算了，写了都写了，就~水~写篇教程吧...）

## 安装

```sh
git clone https://github.com/mumu-lhl/yt-music-archive
cd yt-music-archive
sudo make
```

或者从 AUR 安装：

```sh
yay -S yt-music-archive
# or
paru -S yt-music-archive
```

## 使用

```sh
yt-music-archive save <ID>           # 下载音乐并将 ID 存入存档文件，默认音乐保存在 ~/Music

yt-music-archive save <ID> -p <PATH> # 指定音乐保存到哪个路径下

yt-music-archive fetch               # 根据存档的 ID 拉取音乐，默认音乐保存在 ~/Music
yt-music-archive fetch -p <PATH>     # 指定音乐保存到哪个路径下
```

## 配置

目前可配置的选项不多，可以编辑 `/etc/yt-music-archive/yt-music-archive.conf` 或 `~/.config/yt-music-archive/yt-music-archive.conf`：

```sh
archive_file=~/.local/share/yt-music-archive/archive 
                        # 存储 id 的存档文件
#default_path=~/Music   # 默认保存音乐的路径
```

## 结语

I'm a 🤡.
