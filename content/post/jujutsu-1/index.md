+++
title = 'Jujutsu入门教程：比Git更好用的VCS'
slug = 'jujutsu-1'
date = 2025-07-10T21:59:21+08:00
description = 'Jujutsu是一款强大的VCS，从Git、Mercurial和Darcs等VCS取经，好用程度远远超过Git'
keywords = ['jujutsu', 'git', 'vcs', '教程']
tags = ['jujutsu', 'git', 'vcs', '教程']
categories = ['Tech']
+++

## Jujutsu 是啥？

Jujutsu（简称 jj）是一款功能强大的软件项目版本控制系统。你可以用它来获取代码副本，跟踪代码变更，最后发布这些变更供他人查看和使用。无论你是新手还是老手，无论你是独自开发全新的项目，还是拥有庞大历史和团队的大型软件项目，它都能让你轻松上手。

- 与 Git 兼容
- 匿名分支
- 高度自动化的操作（如自动提交）
- 使用中会有很多友好的提示和指引

Jujutsu 目前基本可用，还缺乏少量功能，如 git submodule 的兼容、tag 的创建。

我会写一个系列的 Jujutsu 教程，这篇教程是本系列教程的第一篇，只讲一些满足日常操作的东西，学了本篇教程，日常就可以正常使用 Jujutsu 了。在使用 Jujutsu 的过程中，如果出了什么问题，优先看 Jujutsu 自己给的解决方案。

## 安装

安装教程见 [Jujutsu docs - Installation and setup](https://jj-vcs.github.io/jj/latest/install-and-setup/)，我用 Fedora，只能用 carogo-binstall 安装，用这个工具安装不需要从源码编译一遍，会直接从 GitHub 上下载已经编译好的二进制文件，并且 cargo-install-update 也支持通过这个工具更新安装的二进制文件。

## 初始化仓库

无论你是从零开始，还是在现有 Git 仓库的基础上进行初始化，都推荐用下面这个命令：

```bash
jj git init --colocate .
```

> 该命令可以从 `.git` 初始化 `.jj` 或者同时初始化 `.jj` 和 `.git`。如果用 `jj init` 初始化，会只有 `.jj`，但 Jujutsu 目前又无法从 `.jj` 初始化 `.git`，用 `git init` 自己手动初始化一个也没用，会没法从 `.jj` 同步。Jujutsu 目前不支持创建 tag，如果要创建 tag，就要用到 Git，如果没有 `.git`，就会很抓狂，虽然可以解决，但要麻烦一点，别问，问就是踩坑了。

## 配置名称和邮箱

```bash
$jj config set --user user.name "Example"
$ jj config set --user user.email "example@example.com"
```

## 第一个提交

Jujutsu 中你不需要 **提交（commit）** ，但是需要 **describe** ，在仓库下做了一些改动后，或改动之前，或改动中，都可以用 describe 命令来解释改动，这个命令的简写是 desc：

```bash
$ jj desc -m "First commit"
Working copy  (@) now at: pwquumlq ae3142c8 First commit
Parent commit (@-)      : zzzzzzzz 00000000 (empty) (no description set)
```

**Working copy** 指的是当前正在工作的 copy，可以任意更改，如果是 Git 的话，如果要修改一个 commit，就要手动操作下，这就体现了 Jujutsu 的自动化。`pwquumlq` 是 Jujutsu 的 id，`ae3142c8` 是对应的 Git 的 id。在之后的操作中可以用 `@` 指代。

**Parent commit** 指的是上一个提交（父提交）。这里的 `zzzzzzzz 00000000` 说明父提交是 **根（root）** 。`(empty)` 表示这个 commit 是空的。`(no description set)` 表示这个 commit 没有 description。在之后的操作中可以用 `@-` 指代（多了一个减号）。

在终端中你会看到 `zzzzzzzz` 中的第一个 `z` 是高亮的，working copy 的 id 的第一个字母也是高亮的，**高亮的字母** 是 commit id 的简写，可以用来指代该 commit。直接高亮出来，方便很多，用终端操作的要比 GUI 快。

## 移动到新的 working copy

一个 commit 已经完成了，然后我们要移动到新的 working copy 进行接下来的工作：

```bash
$ jj new
Working copy  (@) now at: qzmssurw 17a91560 (empty) (no description set)
Parent commit (@-)      : pwquumlq ae3142c8 First commit
```

或者你可以在改动完后把 `describe` 和 `new` 操作用 `commit` 命令一起完成，有多种方案，很灵活：

```bash
$ jj commit -m "First commit"
```

### 分叉

`new` 命令还可以从一个提交 **分叉** 出去，在用下面讲到的查看历史提交中就会看到分成了两条链：

```bash
$ jj new [jj id]
```

## 查看历史提交

可以直接运行 `jj` 来查看历史提交，相当于 `jj log`，输出的结果会省略一部分提交，这里提交数量太少了，Jujutsu 不会省略：

```bash
$ jj
@  qzmssurw mumulhl.666@gmail.com 2025-07-10 21:04:39 17a91560
│  (empty) (no description set)
○  pwquumlq mumulhl.666@gmail.com 2025-07-10 20:56:41 git_head() ae3142c8
│  First commit
◆  zzzzzzzz root() 00000000
```

这是省略的样子，`(elided revisions)` 会替代掉一部分提交：

```bash
$ jj
@  ovqtlklr mumulhl.666@gmail.com 2025-07-09 21:07:36 030cb30c
│  (empty) (no description set)
◆  ruvroqot mumulhl.666@gmail.com 2025-07-09 21:07:26 main git_head() 59603f61
│  Sync vibe coding rules
~  (elided revisions)
│ ○  zsxqkykv mumulhl.666@gmail.com 2025-06-28 16:38:36 edf292f1
╭─┤  (empty) Merge pull request #413 from mumu-lhl/push-xpztozvsopvq
◆ │  xpztozvs mumulhl.666@gmail.com 2025-06-28 16:30:59 9fcc6e8e
│ │  Add changelog button to about page
~ │  (elided revisions)
│ ○    uvknsurt mumulhl.666@gmail.com 2025-06-28 15:48:48 70913779
│ ├─╮  (empty) Merge pull request #411 from weblate/weblate-ciyue-app
......
```

查看完整的历史提交也是没问题的：

```bash
$ jj log -r 'all()'
@  ovqtlklr mumulhl.666@gmail.com 2025-07-09 21:07:36 030cb30c
│  (empty) (no description set)
◆  ruvroqot mumulhl.666@gmail.com 2025-07-09 21:07:26 main git_head() 59603f61
│  Sync vibe coding rules
◆    smoxoopp mumulhl.666@gmail.com 2025-07-08 21:56:35 4403614c
├─╮  (empty) Merge pull request #431 from mumu-lhl/push-xmlopqwnzwuo
│ ◆  xmlopqwn mumulhl.666@gmail.com 2025-07-08 21:52:31 261fa48b
│ │  Refactor Consumer to Selector on wordbook page
│ ◆  tmklvsky mumulhl.666@gmail.com 2025-07-08 21:31:40 af9826b7
├─╯  Add multiselectmode on wordbook page
◆    wlprsvrq mumulhl.666@gmail.com 2025-07-08 13:44:04 d31b7cf5
├─╮  (empty) Merge pull request #430 from mumu-lhl/push-kkmymxosqwvr
│ ◆  kkmymxos mumulhl.666@gmail.com 2025-07-08 11:17:33 0245d789
├─╯  Add storage management
◆    unquwrst mumulhl.666@gmail.com 2025-07-08 08:42:11 7e6a3a76
├─╮  (empty) Merge pull request #428 from mumu-lhl/dependabot/pub/gpt_markdown-1.1.2
│ ◆  svqpznzp 49699333+dependabot[bot]@users.noreply.github.com 2025-07-08 08:28:35 e70cca3f
│ │  chore(deps): bump gpt_markdown from 1.1.1 to 1.1.2
......
```

## 编辑提交

Jujutsu 中提交可以被轻松的编辑：

```bash
$ jj edit @- # 编辑父提交
```

编辑完后回去：

```bash
$ jj next --edit
```

是不是要比 Git 简单得多？

### Squash

我们想要编辑的提交，往往都是父提交，例如忘了 format 或写错了一小点东西的时候，我们可以用 `squash` 更快的完成这步操作，这个命令是把当前 workng copy 的更改合并到父提交上：

```bash
$ jj squash
```

运行完这行命令后，当前 working copy 就会变成空的，因为合并到了父提交中。

## 推送提交

先加 remote，因为 Jujutsu 是与 Git 兼容的，所以可以直接推送到 Github 上，~不需要 JJhub~：

```bash
$ jj git remote add origin [远程仓库地址]
```

Jujutsu 中有 **分支（Branch）** 的平替概念—— **书签（Bookmark）**，但是书签一旦被 set，不会跟随当前的 working copy 的变动而改变，需要手动再次 set，才会变。

现在我们的仓库连 main 书签都没有，这个命令可以把书签 set 到当前 commit 上，如果不存在会自动创建。确保当前的 working copy 不是空的，如果是的话可以用 `jj edit @-` 编辑父提交，使当前的 working copy 变成父提交：

```bash
$ jj b s main # b 是 bookmark 的缩写，s 是 set 的缩写
```

推送到远程仓库：

```bash
$ jj git push
```

当对某个过去的提交更改后，推送是不需要加 --force 之类的参数的，爽了很多。

## 抛弃一个提交

如果你不想要当前 working copy 的更改或最新的提交，可以抛弃掉：

```bash
$ jj abandon [jj id]
```

**注意：不要随意抛弃过去的提交，会导致冲突，很难处理，如果要合并过去的提交，可以用 `squash` 命令，这个命令更强大的用法将在下一篇教程中讲到。**

## 撤销

当你后悔了一个操作的时候，就需要撤销，撤销操作也是非常的简单：

```bash
$ jj undo
```

### 推送到额外的分支上

不是所有项目我们都会一律把提交 **直接** 推送到 main 分支上，按照 Github flow，至少要推送到其他分支上，然后发一个 PR 合并到 main 上。

只需要在推送到远程仓库的命令上多加几个字符就可以了：

```bash
$ jj git push -c@ # -c 后跟随要推送到仓库的提交，比该提交旧的提交也会一起推送上去，这里用 @ 指代当前 working copy
```

这种方法推送上去的分支名是 `push-[jj id]`，当然可以自己在本地先 set 了书签，然后再用 `jj git push` 推送。我比较懒不太喜欢给分支命名，因为 PR 名称就可以诠释一堆的提交是干什么的了，减少心智负担。

在同一个仓库里（多个维护者），如果每个人提交的都是默认的那种 `push-[jj id]`，完全会乱套，我们可以把默认的分支名变成 `yourname/push-[jj id]` 这样去稍微做一点区分。编辑配置文件 `.config/jj/config.toml`（用 `jj config p` 查看配置文件路径），加入以下内容：

```toml
[templates]
git_push_bookmark = '"example/push-" ++ change_id.short()'
```

## 拉取

远程仓库做了一些更改，需要把提交都拉取到本地仓库：

```bash
$ jj git fetch
```

main 书签 set 的提交也自动变化了，但是通过运行 `jj` 你会看到当前 working copy 并不在 main 的那条链上，所以需要我们自己手动一下去移动到 main 那条链上：

```bash
$ jj new main
```

## 结语

用了一段时间的 Jujutsu 后，Jujutsu 基本已经平替了 Git，只能用一个字来说用的体验——爽。

虽然没有多少有价值的学习资料，边用边学的过程中，基本也用个会了，因为操作起来真的很简单。

下一篇教程会深入讲解 Jujutsu 的使用，敬请期待 :)
