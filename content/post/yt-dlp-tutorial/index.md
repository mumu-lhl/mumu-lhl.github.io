+++
title = 'yt-dlp 教程'
slug = 'yt-dlp-tutorial'
date = 2024-08-10T20:59:35+08:00
description = '介绍 yt-dlp 下载指定品质的视频、嵌入元数据、分离音频等功能'
keywords = ['yt-dlp', '下载', '教程']
tags = ['yt-dlp', '教程']
categories = ['Skill']
+++

[yt-dlp](https://github.com/yt-dlp/yt-dlp) 是一个功能强大的命令行音频、视频下载器。yt-dlp fork 自基于 [youtube-dl](https://github.com/ytdl-org/youtube-dl) 的已不维护的 [youtube-dlc](https://github.com/blackjack4494/yt-dlc)，具有额外的功能和问题修复。

yt-dlp 不仅支持 YouTube，还支持[一千多个网站](https://github.com/yt-dlp/yt-dlp/blob/master/supportedsites.md)。除了下载音频、视频，还能下载封面。

## 安装

可以通过 pip 安装，也可以通过你所用的系统的包管理器安装，还可以到 [release](https://github.com/yt-dlp/yt-dlp/releases) 页面下载可执行文件。

```sh
pip install yt-dlp
# or
pipx install yt-dlp
```

## 使用

### 视频

直接加链接即可。

```sh
yt-dlp "https://www.youtube.com/watch?v=dQw4w9WgXcQ"
```

可以下载一整个播放列表的视频。

```sh
yt-dlp "https://www.youtube.com/playlist?list=PLp8YAQVH95dwCMvzkxUhFy4KWRAtp_awf"
```

还可以下载 m3u8。

```sh
yt-dlp "https://example.com/index.m3u8"
```

列出视频可以下载的格式，也列出了传输协议、格式、分辨率、帧率、大小等信息。

```sh
yt-dlp -F "https://www.youtube.com/watch?v=dQw4w9WgXcQ"
# 与下面的命令等价
yt-dlp --list-formats "https://www.youtube.com/watch?v=dQw4w9WgXcQ"
```

yt-dlp 默认会下载分辨率、帧率等最好的视频，如果要指定相应的分辨率、帧率，要用 `--format-sort`/`-S` 参数。

下载分辨率不优于 720p 的视频，也就是下载 720p 的视频。

```sh
yt-dlp -S "res:720" "https://www.youtube.com/watch?v=dQw4w9WgXcQ"
```

指定视频的容器格式。

```sh
yt-dlp -S "ext:webm" "https://www.youtube.com/watch?v=dQw4w9WgXcQ"
```

用 `,` 连接多个限制条件。

```sh
yt-dlp -S "ext:webm,res:720" "https://www.youtube.com/watch?v=dQw4w9WgXcQ"
```

嵌入元数据，默认不嵌入。

```sh
yt-dlp --embed-metadata "https://www.youtube.com/watch?v=dQw4w9WgXcQ"
```

嵌入封面，默认不嵌入。

```sh
yt-dlp --embed-thumbnail "https://www.youtube.com/watch?v=dQw4w9WgXcQ"
```

嵌入字幕，默认不嵌入。仅支持 **mp4**、**mkv**、**webm** 容器的视频

```sh
yt-dlp --embed-subs "https://www.youtube.com/watch?v=dQw4w9WgXcQ"
```

### 音频

分离出视频中的音频。

```sh
yt-dlp -x "https://www.youtube.com/watch?v=dQw4w9WgXcQ"
# 与下面的命令等价
yt-dlp --extract-audio "https://www.youtube.com/watch?v=dQw4w9WgXcQ"
```

指定音频的格式和品质，品质取值 0-10，0 最佳，10 最差，默认为 5。

```sh
yt-dlp -x --audio-format opus --audio-quality 0 "https://www.youtube.com/watch?v=dQw4w9WgXcQ"
```

嵌入元数据。

```sh
yt-dlp -x --embed-metadata "https://www.youtube.com/watch?v=dQw4w9WgXcQ"
```

### 封面

列出所有封面。

```sh
yt-dlp --list-thumbnails "https://www.youtube.com/watch?v=dQw4w9WgXcQ"
```

下载封面，同时会下载视频。

```sh
yt-dlp --write-thumbnail "https://www.youtube.com/watch?v=dQw4w9WgXcQ"
```

下载所有封面，不会下载视频。

```sh
yt-dlp --write-all-thumbnails "https://www.youtube.com/watch?v=dQw4w9WgXcQ"
```

## 结语

写不出什么结语了(liao) :(
