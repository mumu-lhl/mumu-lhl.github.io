---
title: 没麦怎么办，把手机当作麦克风！（Scrcpy 篇）
description: ""
date: 2025-04-30T14:31:56.036Z
preview: ""
draft: false
tags:
    - Android
    - Scrcpy
    - 工具
    - 教程
    - 音频
    - Windows
categories:
    - Tech
image: ""
keywords:
    - Android
    - Scrcpy
    - Windows
    - 麦克风
    - VB-CABLE
---

很久很久之前写了一篇在 Linux 上如何把 Android 作为麦克风的[教程](./../audio-android-to-linux/)，不久前因为要用 Windows 上的一些软件，装上了 Windows，顺便折腾个在 Windows 上的把 Android作为麦克风的方案。

找了很久，没有找到既方便又开源的方案，一些免费的方案（如 WO Mic）音质不太行，自己摸索了一方，最后用 [Scrcpy](https://github.com/Genymobile/scrcpy) 和 [VB-CABLE](https://vb-audio.com/Cable/) 实现了。关键是 **不需要在 Android 上安装任何软件！**

使用 Scrcpy 把 Android 作为麦克风理论上在 Linux 和 macOS 上也可以，在音频驱动那里会有点不同。

**确保 Android 版本 >= 11**

## 安装

### Scrcpy

首先是 Scrcpy，它将用于从 Android 转发音频，可以在 [Github Release 页面](https://github.com/Genymobile/scrcpy/releases) 下载对应的压缩包再解压，32 位下载 `scrcpy-win32-vX.X.zip`，64 位下载 `scrcpy-win64-vX.X.zip`。

最好的办法是直接通过 Windows 的包管理器 winget 安装：

```sh
winget install --id=Genymobile.scrcpy
```

### VB-CABLE

VB-CABLE 的用途是将音频由输出转到输入。

前往 [VB-CABLE官网](https://vb-audio.com/Cable/)，点击 `VBCABLE_Driver_Pack45.zip` 下载压缩包，解压后，双击执行 `VBCABLE_Setup_x64.exe`（64 位）或 `VBCABLE_Setup.exe`（32 位）安装。然后就好了很么都不用动。*系统 > 声音 > 输入* 应该会自己变成 CABLE Output。

## 正式步骤

### Android 上开启 USB 调试

具体步骤见 [Android Developer 文档](https://developer.android.com/studio/debug/dev-options#Enable-debugging)。

### 运行 Scrcpy 命令

如果通过压缩包获取 Scrcpy 点击解压后目录里的 `open_a_terminal_here.bat` 在该目录下打开终端。通过 winget 安装 Scrcpy 直接打开终端即可。

接着运行：

```sh
scrcpy --no-video --no-control --audio-source=mic-unprocessed
```

第一次运行 Android 上会要求授权，授权即可。

参数解释：

- `--no-video` 不要画面
- `--no-control` 不要控制
- `--audio-source=mic-unprocessed` 将声音源指定为未处理的麦克风音频

声音源还有很多选项，完整选项见 [Audio 文档](https://github.com/Genymobile/scrcpy/blob/master/doc/audio.md#source)，我觉得 `mic-unprocessed` 音质是最好的，也可以试试别的，简单列举几个：

- `mic`: 捕获麦克风
- `mic-unprocessed`: 捕获未处理（原始）的麦克风声音
- `mic-camcorder`: 捕获为录制视频调优的麦克风声音，如果可用，其方向与摄像头相同
- `mic-voice-recognition`: 捕获为语音识别调优的麦克风声音
- `mic-voice-communication`: 捕获为语音通信调优的麦克风声音（例如，如果可用，它会利用回声消除或自动增益控制）

### 改变音谱输出设备

在 *系统 > 声音 > 音量合成器 > 应用* 将 Scrcpy 创建的窗口的 *输出设备* 更改为 *CABLE Input*，现在通过 Android 麦克风输入的音频就不会从扬声器里输出了，会从输入设备 *CABLE Output* 里输出。