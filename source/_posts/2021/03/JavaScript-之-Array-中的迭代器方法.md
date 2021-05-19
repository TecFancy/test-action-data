---
title: JavaScript 之 Array 中的迭代器方法
categories: JavaScript
tags:
  - JavaScript 语法
  - JavaScript 集合引用类型
  - JavaScript Array
date: 2021-03-23 00:15:02
---

`Array` 的原型上暴露了 3 个用于检索数组内容的迭代器方法：`keys()`、`values()` 和 `entries()`。

- `keys()`：返回数组索引的迭代器。
- `values()`：返回数组元素的迭代器。
- `entries()`：返回索引/值对的迭代器。

<!-- more -->

```js
const strings = ["hi-ruofei.com", "https://hi-ruofei.com", "Hello world"];

// 因为这些方法返回的都是迭代器，所以可以将他们的内容
// 通过 Array.from() 直接转换为数组实例
const keys = Array.from(strings.keys());
const values = Array.from(strings.values());
const entries = Array.from(strings.entries());

console.log(keys); // [0, 1, 2]
console.log(values); // ['hi-ruofei.com', 'https://hi-ruofei.com', 'Hello world.'];
console.log(entries); // [[0, 'hi-ruofei.com'], [1, 'https://hi-ruofei.com'], [2, 'Hello world']]
```

使用 ES6 的解构可以非常容易地在循环中拆分键/值对：

```js
const strings = ["hi-ruofei.com", "https://hi-ruofei.com", "Hello world"];
const entries = Array.from(strings.entries());
const result = {};

for (const [idx, element] of entries) {
  result[element] = { idx };
}

/**
 * result:
 * {
 *   'hi-ruofei.com': { idx: 0 },
 *   'https://hi-ruofei.com': { idx: 0 },
 *   'Hello world': { idx: 0 },
 * }
 */
console.log(result);
```
