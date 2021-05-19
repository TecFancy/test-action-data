---
title: 安装 Node.js
categories: Node.js
date: 2021-01-27 23:20:20
tags:
---

## 通过软件包安装 Node.js

这是最简单的安装方式，通过下载官网编译好的软件包，可直接在本地计算机中安装 Node.js。要下载各平台支持的软件包，请访问[这里](https://nodejs.org/en/download/)。

<!-- more -->

## 通过包管理器安装 Node.js

另一种便捷的方式是通过软件包管理器安装 Node.js。每种操作系统都有其自身的包管理器，适用与 Linux 和 windows 的包管理器列出在 [https://nodejs.org/en/download/package-manager/](https://nodejs.org/en/download/package-manager/)。

在 MacOS 上，可通过 Homebrew 安装：

```bash
brew install node
```

{% note info %}
安装 Homebrew 总是失败怎么办？

```bash
/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"
```

在中国大陆使用这个仓库安装 Homebrew 会非常快，该仓库会每天从 GitHub 同步一次，可放心使用。
{% endnote %}

## 通过 nvm 安装 Node.js

nvm 是一种流行的运行 Node.js 的方式，使用 nvm 可轻松地切换 Node.js 版本。nvm 的安装使用请参考[说明文档](https://github.com/nvm-sh/nvm#installing-and-updating)。

## 验证

无论哪种方式，当安装 Node.js 之后，就可以在命令行中访问 `node` 可执行程序和 `npm` 包管理器。

```bash
$ node -v
v14.15.3

$ npm -v
6.14.9
```
