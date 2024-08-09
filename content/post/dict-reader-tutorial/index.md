+++
title = 'Dart 中读取 mdict 词典教程'
slug = 'dict-reader-tutorial'
date = 2024-08-09T17:56:10+08:00
description = '在 Dart 中用 dict_reader 读取 mdict 词典'
keywords = ['dart', 'mdict', 'MDX', 'MDD', '教程', '词典']
tags = ['教程', 'dart']
categories = ['Tech']
+++

[Dict_reader](https://github.com/mumu-lhl/dict_reader) 是一个用于读取 mdict 词典的 Dart 语言库，支持 MDX/MDD 格式。

README 提供了几个示例，本文提供更适合生产环境的示例。

## 安装

```sh
dart pub add dict_reader
```

## 使用

在生产环境中，会用到搜索单词、查看单词的功能，而且必须要高效，这时候轮到 SQLite 数据库出场了。在第一次读取词典时，用 Drift 存储少量数据，用于之后快速地搜索和查看。

### 安装 Drift

```sh
dart pub add drift drift_flutter dev:drift_dev dev:build_runner
```

### 示例

```dart
// database.dart
import "package:dict_reader/dict_reader.dart";
import "package:drift/drift.dart";
import "package:drift/native.dart";
import "dart:io";

part 'database.g.dart';

@TableIndex(name: 'idx_keyText', columns: {#keyText})
class Dictionary extends Table {
  TextColumn get keyText => text()();
  IntColumn get recordBlockOffset => integer()();
  IntColumn get startOffset => integer()();
  IntColumn get endOffset => integer()();
  IntColumn get compressedSize => integer()();
}

@DriftDatabase(tables: [Dictionary])
class AppDatabase extends _$AppDatabase {
  // After generating code, this class needs to define a `schemaVersion` getter
  // and a constructor telling drift where the database should be stored.
  // These are described in the getting started guide: https://drift.simonbinder.eu/getting-started/#open
  AppDatabase() : super(_openConnection());

  @override
  int get schemaVersion => 1;

  Future<void> insertUsers(List<DictionaryCompanion> dictionary) async {
    await batch((batch) {
      batch.insertAll(this.dictionary, dictionary);
    });
  }

  Future<List<DictionaryData>> searchWord(String word) {
    return (select(dictionary)..where((u) => u.keyText.like('$word%'))).get();
  }

  static QueryExecutor _openConnection() {
    return NativeDatabase(File('dictionary.db'));
  }
}

void main() async {
  final database = AppDatabase();
  final dictReader = DictReader("MDX FILE PATH");

  // 不用获取 keyText 和 offset 存入数据库时，可以传入 false 参数
  await dictReader.init();

  // 将 keyText 和 offset 存入数据库，只需一次
  var queue = <DictionaryCompanion>[];
  await for (final (
        keyText,
        (recordBlockOffset, startOffset, endOffset, compressedSize)
      ) in dictReader.read()) {
    queue.add(DictionaryCompanion(
        keyText: Value(keyText),
        recordBlockOffset: Value(recordBlockOffset),
        startOffset: Value(startOffset),
        endOffset: Value(endOffset),
        compressedSize: Value(compressedSize)));
  }

  await database.insertUsers(queue);

  // 通过数据库搜索单词
  final result = (await database.searchWord("go"))[0];
  // 获取单词数据
  print(await dictReader.readOne(result.recordBlockOffset, result.startOffset,
      result.endOffset, result.compressedSize));

  await database.close();
}
```

然后生成 database.g.dart 文件：

```sh
dart run build_runner build
```

这个示例不适合直接放到生产环境中，稍微改一下就可以了。

## 结语

Dict_reader 主要是翻译 [mdict-analysis](https://bitbucket.org/xwang/mdict-analysis/)。我对 Dart 不是很熟，在翻译过程中经常去问 gpt-4o-mini 以及 [SearchGPTool](https://searchgptool.ai)，有帮助也有捣乱，最终还是花了四天时间完成。

Dict_reader 并没有完全翻译，而且挑选了最重要、有意义的部分翻译，也基于我自身考虑（我手头没有 mdict 格式 3.0 版本的词库），例如没有校验、不支持 lzo 压缩、不支持 mdict 格式的 3.0 版本，但不影响一般使用。
