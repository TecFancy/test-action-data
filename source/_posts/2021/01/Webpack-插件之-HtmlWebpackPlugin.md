---
title: Webpack 插件之 HtmlWebpackPlugin
categories: Webpack
tags: 插件
date: 2021-01-27 23:29:23
---

该插件将为你生成一个 HTML5 文件，用以服务 webpack 编译输出的 bundle。

## 安装

```bash
npm install --save-dev html-webpack-plugin # or yarn add --dev html-webpack-plugin
```

<!-- more -->

## 基础用法

该插件会为你生成一个 HTML5 文件，在文件的 `body` 标签中包含 webpack 编译输出的所有 bundle 文件。你只需要在 webpack 配置文件中启用该插件即可：

```js
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");

module.exports = {
  entry: "index.js",
  output: {
    path: path.resolve(__dirname, "./dist"),
    filename: "index_bundle.js",
  },
  plugins: [new HtmlWebpackPlugin()],
};
```

webpack 编译后会在 dist 目录下创建一个 index.html 文件，文件中将包含以下内容：

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title>webpack App</title>
  </head>
  <body>
    <script src="index_bundle.js"></script>
  </body>
</html>
```

## 配置

要查看该插件所有配置项，参考[插件文档](https://github.com/jantimon/html-webpack-plugin#plugins)。
