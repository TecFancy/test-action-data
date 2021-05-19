---
title: JavaScript 基础语法
tags: JavaScript 语法
categories: JavaScript
date: 2021-01-17 17:10:20
---

ECMAScript 的语法大量借鉴了 C 及其他类 C 语言（如 Java 和 Perl）的语法，而 ECMAScript 语法相对其他语言的语法要更加宽松。

<!-- more -->

## 区分大小写

ECMAScript 中的一切（变量、函数名和操作符）都区分大小写。比如，变量名 `test` 和变量名 `Test` 分别表示完全不同的两个变量。再比如，`typeof` 不能作为变量名（因为它是 ECMAScript 中的一个关键字，关键字不能用作函数名），但是 `typeOf` 则完全可以是一个有效的函数名。

## 标识符

**标识符**是指变量、函数、属性的名字，或者函数的参数。书写标识符有以下规则：

- 第一个字符必须是一个字母、下划线（`_`）或一个美元符号（`$`）；
- 除第一个字符外的其他字符可以是字母、下划线、美元符号或数字。

按照惯例，书写标识符应采用驼峰大小写格式。例如：

```javascript
myHouse;
doSomething;
firstNumber;
```

{% note info %}

不能把关键字、保留字、`true`、`false` 和 `null` 用作标识符。

{% endnote %}

## 注释

ECMAScript 中有两风格的注释，分别是单行注释和块级注释。例如：

```javascript
// 单行注释
```

块级注释以一个斜杠和一个星号（`/*`）开头，以一个星号和一个斜杠（`*/`）结尾。例如：

```javascript
/*
 * 多行注释
 * （块级）注释
 */
```

第二行和第三行开始的星号不是必须的，这样写纯粹是为了提高注释的可读性。

## 严格模式

要在脚本中启用严格模式，可以在文件顶部添加以下代码：

```javascript
"use strict";
```

除此以外，还可以在函数内部启用严格模式，指示该函数在严格模式下运行：

```javascript
function doSomething() {
  "use strict";
  // do something...
}
```

## 语句

ECMAScript 中的语句以一个英文分号（`;`）结尾；若省略分号，则由解析器确定语句的结尾。例如：

```javascript
const sum = a + b; // 没有分号也不要紧，但是不推荐这样的写法
const diff = a - b; // 推荐每条语句后都要写分号
```
