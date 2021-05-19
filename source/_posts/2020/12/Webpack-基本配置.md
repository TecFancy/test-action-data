---
title: Webpack 基本配置
tags: Webpack
date: 2020-12-13 16:41:15
---

## 初始化项目目录

首先我们创建一个目录，初始化 npm，然后在本地安装 webpack，接着安装 `webpack-cli`：

```bash
mkdir webpack-demo
cd webpack-demo
npm init -y
npm install webpack webpack-cli html-webpack-plugin --save-dev # 或 yarn add --dev webpack webpack-cli html-webpack-plugin
```

{% note info %}
`webpack-cli` 工具用于在命令行中运行 webpack。[`html-webpack-plugin`](/development/webpack/plugins/HtmlWebpackPlugin) 是 webpack 的一个自动生成 HTML5 文件的插件。
{% endnote %}

<!-- more -->

现在，我们将创建以下目录结构、文件和内容：

{% tabs 初始化项目目录 %}

<!-- tab project -->

```none
webpack-demo
|- package.json
|- config/
  |- webpack.config.js
|- public/
  |- index.html
|- src/
  |- index.js
```

这是我们的项目目录结构。

<!-- endtab -->
<!-- tab package.json -->

```json
{
  "name": "webpack-demo",
  "version": "0.1.0",
  "description": "Webpack demo",
  "scripts": {
    "build": "webpack --config config/webpack.config.js"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {},
  "devDependencies": {
    "html-webpack-plugin": "^4.5.0",
    "webpack": "^5.10.1",
    "webpack-cli": "^4.2.0"
  }
}
```

在命令行执行 `npm run build` 或 `yarn build` 命令时，webpack 会使用 config 目录下的 `webpack.config.js` 配置文件中的配置项来编译项目。

<!-- endtab -->
<!-- tab public/index.html -->

```html
<!DOCTYPE html>
<html lang="zh">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Webpack demo</title>
  </head>
  <body>
    Hello world.
  </body>
</html>
```

该文件为模板文件，当 webpack 编译后会把该文件复制到 webpack 配置的输出目录中。

<!-- endtab -->
<!-- tab src/index.js -->

```js
console.log("Hello world.");
```

这就是我们项目的入口文件了。

<!-- endtab -->

{% endtabs %}

## Webpack 基本配置

在 config 目录下的 webpack.config.js 文件中填写以下内容：

```js
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");

module.exports = {
  entry: path.resolve(__dirname, "../src/index.js"),
  output: {
    filename: "main.js",
    path: path.resolve(__dirname, "../dist"),
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: path.resolve(__dirname, "../public/index.html"),
    }),
  ],
};
```

- `entry`：webpack 的[入口起点](https://webpack.docschina.org/concepts/entry-points/)，这里我们将 src 目录下的 index.js 文件作为入口起点。
- `output`： webpack 编译后的[输出目录](https://webpack.docschina.org/concepts/output/)，最后编译后的文件将会输出到项目根目录的 dist 文件夹下。
- `plugins`：配置[插件](https://webpack.docschina.org/concepts/#plugins)，可以配置任意多个插件。我们在这里配置了 [HtmlWebpackPlugin](/development/webpack/plugins/HtmlWebpackPlugin/) 插件。该插件会通过配置项中的 `template` 属性将 public/index.html 文件复制到 dist 目录下，并将 webpack 编译生成的 bundle 插入到文件中。

## 验证

执行 `npm run build` 或 `yarn build`：

```bash
$ npm run build
[webpack-cli] Compilation finished
asset index.html 225 bytes [emitted]
asset main.js 28 bytes [compared for emit] [minimized] (name: main)
./src/index.js 29 bytes [built] [code generated]
webpack 5.10.1 compiled successfully in 259 ms
✨  Done in 1.38s.
```

编译结束后，项目根目录项会被创建出一个 dist 目录，目录结构应该是这样的：

```none
dist/
|- index.html
|- main.js
```

在 index.html 的 `body` 标签中，我们会发现 main.js 文件已经被正确引入进来了。现在我们用浏览器打开 index.html 文件，会在页面中显示 `Hello world.` 且控制台中会打印 `Hello world.` 消息。

至此，一个最基本的 webpack 配置已经完成，有任何想法或疑问欢迎在下方留言讨论。
