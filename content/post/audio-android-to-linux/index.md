+++
title = '将 Android 作为 Linux 的麦克风'
slug = 'audio-android-to-linux'
date = 2024-08-11T12:40:00+08:00
description = '用 Audio Source 将 Android 作为 Linux 的麦克风'
keywords = ['教程', 'linux', 'android', '音频', '麦克风']
tags = ['教程', 'android']
categories = ['Skill']
+++

你的电脑可能没有麦克风，需要的时候，又不想买一个麦克风，这时候就可以把你的 Android 当作麦克风来用。

[Audio Source](https://github.com/gdzx/audiosource) 是一个用 ADB 将 Android 麦克风的输入转发到 PulseAudio 进程的工具。

## 准备

* 一只手
* Android 4.0 及以上
* 一条 USB 数据线，充电线也没问题
* PATH 中包含 python3、pactl、adb（Archlinux 如果没有 adb，用 `sudo pacman -S android-tools` 安装，安装后需重启）

## 使用

### Android 端

可在 [Releases](https://github.com/gdzx/audiosource/releases) 页面下载，或在 [IzzyOnDroid F-Droid Repository](https://apt.izzysoft.de/fdroid/index/apk/fr.dzx.audiosource) 下载。

安装后，点开软件，如果没看到 UI 很正常，这个软件就是没有 UI 的... 然后要授权软件麦克风和通知权限，如果授权权限的弹窗闪退，可以在 *设置* 里面授权软件权限。

### Linux 端

下载 audiosource 脚本并授权可执行权限。

```sh
curl -O https://raw.githubusercontent.com/gdzx/audiosource/master/audiosource && chmod +x audiosource
```

用 USB 数据线连接手机和电脑，在 *开发者选项* 里开启 **USB 调试模式**。

用 adb 查看一下连接的 Android 设备，这时手机会弹出授权窗口，点授权就完事了。授权完再运行一下这个命令，看看是否正常。

```sh
adb devices
```

运行 audiosource 脚本，你的 Linux 就有麦克风啦 :)

```sh
./audiosource run
```

## 结语

Audio Source 是我意外在 F-Droid 找到的，F-Droid 上的宝藏很多 :)
