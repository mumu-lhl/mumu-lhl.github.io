+++
title = 'nginx 搭建极简直播服务'
slug = 'nginx-live-service'
date = 2024-09-30T20:31:22+08:00
description = '在 Linux 上使用 nginx 搭建极简直播服务'
keywords = ['nginx', 'Linux', '直播', '教程']
tags = ['nginx', 'linux', '教程']
categories = ['Tech']
+++

当我们像在仅需要在局域网内进行直播这种情况时，就可以自己动手搭建极简的直播服务。

## 预备知识

会写一点点 nginx 配置。

## 概念

### RTMP

用于流媒体传输的协议，最初用于 Flash 播放器，也可以用于直播。

本教程中，我们将用该协议将直播流推送至 nginx 服务器。

### HLS

基于 HTTP 流媒体传输协议，它将流分割成多个文件传输，对于直播流，以 m3u8 文件为播放列表，以 ts 文件为视频。

本教程中，该协议用于向浏览器传输直播流。

## 编译 nginx

nginx 本身并不支持 RTMP 协议，需要把 [nginx-rtmp-module](https://github.com/arut/nginx-rtmp-module) 模块编译进 nginx。

从 [nginx.org](https://nginx.org/en/download.html) 下载 nginx 源码，解压后进入目录。

然后克隆 nginx-rtmp-module 源码：

```sh
git clone https://github.com/arut/nginx-rtmp-module --depth=1
```

编译并安装 nginx：

```sh
./configure --add-module=nginx-rtmp-module
make
make install
```

最后 `/usr/local/nginx/sbin` 添加到环境变量 `PATH` 中。

## 前端

一般浏览器不能直接播放 HLS，这里采用 DPlayer + hls.js 用于播放 ~（因为好看）~。

从 [这里](https://www.jsdelivr.com/package/npm/dplayer?tab=files&path=dist) 还有 [这里](https://www.jsdelivr.com/package/npm/hls.js?tab=files&path=dist) 下载 `DPlayer.min.js` 和 `hls.min.js` 到 `/usr/local/nginx/html/`。

将 `/usr/local/nginx/html/index.html` 改为：

```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>直播</title>
</head>

<body>
  <div id="dplayer"></div>
  <script src="hls.min.js"></script>
  <script src="DPlayer.min.js"></script>
  <script>
    const dp = new DPlayer({
      container: document.getElementById('dplayer'),
      autoplay: true, // 自动播放
      live: true,     // 直播
      video: {
        url: '/live/example.m3u8', // 等下 example 改成自己的推流码
        type: 'hls',
      },
    });
  </script>
</body>

</html>
```

打开首页就是播放器。

## 配置

将 `/usr/local/nginx/html/conf/nginx.conf` 改为：

```nginx
worker_processes  1;


events {
    worker_connections  1024;
}

rtmp {
    server {
        listen 1935;

        chunk_size 4000;

        application live {      # /live 是推流地址
            live on;
            hls on;
            hls_path html/live;
        }
    }
}

http {
    server {
        listen      8080;

        location / {
            root html;
            add_header Cache-Control no-cache;
        }
    }
}
```

关于 nginx-rtmp-module 更多配置见 [wiki](https://github.com/arut/nginx-rtmp-module/wiki/Directives)。

不用担心 HLS 的 ts 文件会无限增加下去，nginx-rtmp-module 会删除老的 ts 文件。

启动 nginx：

```sh
nginx
```

## 推流

这里推流使用 [OBS Studio](https://obsproject.com)。

在“设置”-“直播”中填写“服务器”为 `http://localhost:8080/live`，“推流码”填自己想要的，但 `index.html` 中的 `example` 要改成这个推流码，不然没法播放直播。

配置好来源就可以开始直播啦~

## 试试看

打开 `http://localhost:8080` 就可以看到直播了，大概会有 10s 左右的延迟。

正式使用时，请根据自己的需求更改。

## 结语

过去，学校里的大型表演，能到现场看的人数有限，不是所有人都能到现场看，而且没有直播。再过不久有一个歌唱比赛（我当然去参加海选了，只不过没选上），我想搭建一个直播服务去直播这些表演，于是就有了这篇教程。因为能否直播还要征得校方同意，所以不一定能用得上...
