+++
title = 'PKGBUILD 编写、发布、自动更新教程'
slug = 'pkgbuild-tutorial'
date = 2024-07-25T19:54:02+08:00
description = '简易 PKGBUILD 编写、发布、自动更新教程'
keywords = ['Archlinux', 'PKGBUILD', '教程']
tags = ['Archlinux', 'PKGBUILD', '教程']
categories = ['Tech']
+++

## 概念
### PKGBUILD
PKGBUILD 文件采用 Bash 语法，用于 Archlinux 及其衍生发行版构建软件包，用户可以将自己编写的 PKGBUILD 发布到 [AUR(Arch User Repository)](https://aur.archlinux.org)，让其他用户构建并安装软件包（通常由工具完成，如 yay、paru）。

编写 PKGBUILD 只需要 Bash 的 **创建变量**、**读取变量**、**声明函数**、**创建数组** 等少量知识即可，可以到 [网道 WangDoc](https://wangdoc.com/bash/) 学习。

### makepkg
读取 PKGBUILD 构建软件包的工具。

## 辅助工具
这些工具在后文会介绍具体，请先用 pacman 安装，可以简化 PKGBUILD 的维护：
* devtools - 包含的 pkgctl
* nvchecker - 为 pkgctl 提供版本检测
* namcap - 检测 PKGBUILD 常见的错误
* updpkgsums - 自动下载软件来源，计算 hash 填入 PKGBUILD（不用另外安装）

```sh
sudo pacman -S devtools nvchecker namcap
```

这些工具你可能需要（不要的话关系也不大），使用自行看其文档：
* [termux-language-server](https://github.com/termux/termux-language-server) - 为 PKGBUILD 提供语言服务器（Language Server）

## 基本格式
你可以在 `/usr/share/pacman` 目录下找到三个没有注释的 PKGBUILD 示例文件，其中 PKGBUILD.pro 应该是最有用的。

```sh
# https://wiki.archlinux.org/title/Arch_package_guidelines
# Maintainer: Your Name <youremail@domain.com>
pkgname=NAME    # 名称
pkgver=VERSION  # 版本
pkgrel=1        # 通常为 1，不需要更改，当版本不变需要让用户重新安装软件包时
                #（如 PKGBUILD 增加补丁、修复 bug），需要加 1。
pkgdesc=""      # 介绍
arch=()         # 适用的架构，如 x86_64、aarch64、i386，也可以是 any，表示软件不受架构限制
url=""          # 软件主页链接
license=("GPL") # 许可证
groups=()       # 归属的软件包组，基本用不到
depends=()      # 软件运行所需的依赖
makedepends=()  # 构建软件所需的依赖
optdepends=("package_name: description")
                # 软件运行可选的依赖
provides=()     # 提供的功能
conflicts=()    # 与什么功能冲突，通常与 provides 的值相同
replaces=()     # 该包安装时替换什么包，基本用不到
backup=()       # 该包安装或卸载时，要备份的文件的路径，
                # 使用相对路径（如 etc/pacman.conf），通常是配置文件。
                # 小知识：
                # 升级时包自带的要备份的文件将会以 .pacnew 后缀保存，不覆盖本地的；
                # 卸载时本地的要备份的文件会以 .pacsave 后缀重新命名
options=()      # makepkg 选项，具体参数在
                # https://man.archlinux.org/man/PKGBUILD.5#OPTIONS_AND_DIRECTIVES
changelog=      # 软件更新日志，基本都不写的
source=(FILENAME::URL)
                # 不定构架，软件来源（可以是压缩文件，也可以是 git 仓库地址，写法见下面的 git 示例），
                # FILENAME 用于将下载到的文件命名为它，
                # 可以用上面定义的变量组成，如 $pkgname-$pkgver.tar.gz
                # makepkg 会自动解压，解压后的目录存于变量 srcdir
                # URL 则是指向文件的链接
#source=(URL)   # FILENAME 也可以省略
#source_x86_64  # 相应架构的软件来源
noextract=()    # 需要其他解压工具时，不解压的软件来源，填写这一项需要在 prepare 函数中解压文件
                # 还要在 makedepends 填写解压工具
sha256sums=()   # 不定架构的软件来源的 hash，下文将介绍用 updpkgsums 自动填写，也可以用其他的 hash，如 sha512
#sha256sums_x86_64
                # 特定架构的软件来源的 hash

# pkgver 函数用于获取软件版本，替代 pkgver 变量，通常用于打包直接用 git 拉取仓库进行构建的软件包
#pkgver() {}

# prepare 函数准备软件构建，在 build 函数前执行
#prepare() {}

# build 函数构建软件，在 package 函数前执行
#build() {}

# package 函数安装软件
package() {
  # 安装二进制文件
  # 工作步骤：
  # 1. 将一个文件复制到另一个文件
  # 2. 赋予复制后的文件可执行权限
  install -Dm755 ${srcdir}/binary ${pkgdir}/usr/bin/binary # srcdir 变量是软件来源解压后的目录
                                                           # pkgdir 变量是一个存放被打包的文件的目录
}
```

## 自动填写 hash
```sh
updpkgsums
```

## 示例
请根据自己要打包的软件选择示例，也可以直接跳到 [如何发布](#发布)。
### 二进制
见 [基本格式](#基本格式)

### git 从仓库拉取构建
只写出一些与打包二进制的不同的地方。

```sh
pkgname=NAME-git    # 名称应该以 -git 结尾
pkgver=1            # 先随便填个版本进去，等下构建时 makepkg 会自动填写 pkgver 函数生成的版本
sha256sums=("SKIP") # 因为拉取最新的仓库所以无法指定 hash，直接跳过检查 hash
source=("git+https://URL")

pkgver {
  # 从 git 仓库生成软件版本
  # 其他实现见: https://wiki.archlinux.org/title/VCS_package_guidelines#Git
  cd "$pkgname"
  ( set -o pipefail
    git describe --long --abbrev=7 2>/dev/null | sed 's/\([^-]*-g\)/r\1/;s/-/./g' ||
    printf "r%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short=7 HEAD)"
  )
}
```

### 其他
如 pip、npm、cargo 等编程语言的软件包的打包是不需要手写 PKGBUILD 的，用脚本生成就可以了，见 [Arch Wiki](https://wiki.archlinux.org/title/Creating_packages#PKGBUILD_generators)。

## 测试
### 构建
写完 PKGBUILD 最好测试一下能否正常构建软件。

```sh
makepkg
```

这是最简单的测试方法，但有个问题，因为安装了很多包，系统是不“干净”的，如果 PKGBUILD 中有依赖没写上去，也会正常构建，为了避免这种情况，可以用 pkgctl 构建。

```sh
pkgctl build
```

pkgctl 会自动在一个“干净”的 chroot 里下载安装依赖并构建软件。而且还会生成 `.SRCINFO` 文件，这是发布至 AUR 必须要有的文件。

### 安装
```sh
makepkg --install
```

## 发布
如果测试没问题的话就可以发布了。首先的首先，你需要一个 AUR 账号，这个步骤很简单，就不写了。

### 验证
创建 AUR 专用的 SSH 密钥。

```sh
ssh-keygen -f ~/.ssh/aur
```

在 `~/.ssh/config` 中加入这些内容：

```
Host aur.archlinux.org
  IdentityFile ~/.ssh/aur
  User aur
```

最后在 AUR 账号的设置页面加入 `~/.ssh/aur.pub` 里的公钥就好了。

### 生成 `.SRCINFO` 文件

如果不是用 pkgctl 测试构建软件包，那么需要这条命令生成 `.SRCINFO` 文件：

```sh
makepkg --printsrcinfo > .SRCINFO
```

### 创建包仓库
AUR 只接受 master 分支的推送。

```sh
# 如果还没有 git 仓库
git -c init.defaultbranch=master clone ssh://aur@aur.archlinux.org/pkgbase.git

# 如果已有 git 仓库
git switch -c master
## 如果有了提交
git branch -d 
```

### 推送
这一步不用我多说吧 :)

## 自动更新
根据 PKGBUILD 生成自动更新的配置 `.nvchecker.toml`：

```sh
pkgctl version setup
```

更新 PKGBUILD 中的 pkgver：

```sh
pkgctl version upgrade
```

再用 updpkgsums 自动填写 hash，非常的完美！

## 结语
这篇文章写起来超费时间，比写三篇小短文的总时间还要多 :(

因为找不到 PKGBUILD 编写、发布、自动更新一体化的教程，所以就自己写啰。

这篇文章介绍编写的 PKGBUILD 比较简单，不是很完整，剩下就是下一篇文章的事了 :)
