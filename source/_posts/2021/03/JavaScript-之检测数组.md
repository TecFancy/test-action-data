---
title: JavaScript 之检测数组
categories: JavaScript
tags:
  - JavaScript 语法
  - JavaScript 集合引用类型
  - JavaScript Array
date: 2021-03-24 22:47:50
---

要判断一个对象是不是数组，ECMAScript 提供了 2 种检测方式，他们分别是：

- `instanceof` 操作符
- `Array.isArray()` 方法

<!-- more -->

在只有一个全局作用域的情况下，使用 `instanceof` 操作符即可：

```js
if (value instanceof Array) {
  // 操作数组
}
```

使用 `instanceof` 的问题是假设只有一个全局上下文。如果网页中有多个框架，则可能存在两个以上全局执行上下文，因此可能会有多个不同的 `Array` 构造函数。当从一个框架中将数组传递给另外一个框架时，则这个数组的构造函数将有别于第二个框架中的数组。

为了解决这个问题，ECMAScript 提供了 `Array.isArray()` 方法。这个方法的作用就是确定一个值是否为数组，而不用管它是在哪个全局上下文中创建的。下面是一个例子：

```js
if (Array.isArray(value)) {
  // 操作数组
}
```
