---
title: Jest
description: 快速开始
comments: false
---

Jest 是一个令人愉快的 JavaScript 测试框架，专注于简洁明快。

它适用但不限于使用以下技术的项目：Babel、TypeScript、Node、React、Angular、Vue。

## 安装

使用 `npm` 安装 Jest：

```bash
npm install --save-dev jest
```

使用 `yarn` 安装 Jest：

```bash
yarn add --dev jest
```

{% note info %}
该文档统一使用 `yarn` 命令，你也可以使用 `npm`。
{% endnote %}

## 在命令行运行

想要在命令行中运行 jest，需要将 jest 安装到全局：

```bash
yarn global add jest # npm install jest --global
```

安装完成之后即可在命令行中运行：

```bash
jest my-test-file --config=config.json
```

{% note info %}
上面演示了如何对能匹配到 my-test-file 的文件运行 jest，使用 config.json 作为配置文件，并在运行完成后显示一个原生的操作系统通知。
{% endnote %}

如果不将 jest 安装到全局环境，而是要在项目中通过命令行执行 jest，可以通过 npx 命令执行：

```bash
npx jest my-test-file --config=config.json
```

除用 npx 执行 jest 外，还可以在项目的 package.json 文件中配置一个脚本：

```json
{
  "scripts": {
    "test": "jest my-test-file --config=config.json"
  }
}
```

之后在项目中打开终端工具，运行以下命令即可：

```bash
yarn test # npm run test
```
