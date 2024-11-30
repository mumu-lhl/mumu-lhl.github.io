---
title: 给Flutter Android App支持全局上下文菜单
description: 某些情况下需要给Flutter Android App支持全局上下文菜单，本文将简洁地告诉你该怎么做
date: 2024-11-30T14:18:25.744Z
preview: ""
draft: false
tags:
    - Android
    - Flutter
    - 教程
categories:
    - Tech
keywords:
    - Android
    - Flutter
    - 上下文菜单
---

最近在开发词悦（一个 mdict 词典）的时候，需要支持全局上下文菜单，查了很多资料都没有找到合适的方法，问了下 cursor，得到了初步方案，经过稍微的改动就有了这篇教程。

本文开发环境在 Linux 下。

## 初始化项目

```sh
flutter create example
cd example
```

## 写代码

### Manifest

编辑 `android/app/src/main/AndroidManifest.xml`:

```xml
<!-- 省略... -->
    <meta-data
        android:name="io.flutter.embedding.android.NormalTheme"
        android:resource="@style/NormalTheme"
        />
    <intent-filter>
        <action android:name="android.intent.action.MAIN"/>
        <category android:name="android.intent.category.LAUNCHER"/>
    </intent-filter>
</activity>
<!-- 新增的部分 -->
<activity
    android:name=".ProcessTextActivity"
    android:label="@string/appName"
    android:exported="true">
    <intent-filter>
        <action android:name="android.intent.action.PROCESS_TEXT" />
        <data android:mimeType="text/plain" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</activity>
```

### 原生 Android

创建 `android/app/src/main/kotlin/com/example/example/ProcessTextActivity.kt`:

```kotlin
package com.example.example

import android.app.Activity
import android.content.Intent
import android.os.Bundle
import io.flutter.embedding.android.FlutterActivity

class ProcessTextActivity : Activity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        val text = intent?.getCharSequenceExtra(Intent.EXTRA_PROCESS_TEXT)?.toString() ?: ""

        val intent = Intent(this, MainActivity::class.java).apply {
            action = Intent.ACTION_PROCESS_TEXT
            putExtra(Intent.EXTRA_PROCESS_TEXT, text)
            addFlags(Intent.FLAG_ACTIVITY_NEW_TASK)
            addFlags(Intent.FLAG_ACTIVITY_CLEAR_TASK) // 如果不加这个 flag，app 在后台运行时无法把选中的文本传给 Flutter
        }

        startActivity(intent)
        finish()
    }
}
```

编辑 `android/app/src/main/kotlin/com/example/example/MainActivity.kt`:

```kotlin
package com.example.example

import android.content.Intent
import android.os.Bundle
import io.flutter.embedding.android.FlutterActivity
import io.flutter.embedding.engine.FlutterEngine
import io.flutter.plugin.common.MethodChannel

class MainActivity: FlutterActivity() {
    private val CHANNEL = "com.example.example/process_text"
    private var methodChannel: MethodChannel? = null
    
    override fun configureFlutterEngine(flutterEngine: FlutterEngine) {
        super.configureFlutterEngine(flutterEngine)
        
        methodChannel = MethodChannel(flutterEngine.dartExecutor.binaryMessenger, CHANNEL)
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        
        if (intent?.action == Intent.ACTION_PROCESS_TEXT) {
            val text = intent.getCharSequenceExtra(Intent.EXTRA_PROCESS_TEXT)?.toString() ?: ""
            methodChannel?.invokeMethod("processText", text)
         }
    }
}
```

### Flutter 部分

编辑 `lib/main.dart`:

```dart
import 'package:flutter/material.dart';
import 'package:flutter/services.dart';

void main() {
  platform.setMethodCallHandler((call) async {
    if (call.method == "processText") {
      final text = call.arguments as String; // call.arguments 里就是选中的文本了
      print(text);
    }
  });

  runApp(const MyApp());
}

const platform = MethodChannel("com.example.example/process_text");
```

## 结尾

接下来怎么样就靠你的想象力了 :)