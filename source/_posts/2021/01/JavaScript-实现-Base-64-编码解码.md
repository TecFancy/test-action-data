---
title: JavaScript 实现 Base-64 编码解码
categories: JavaScript
tags:
  - btoa
  - atob
date: 2021-01-04 13:58:55
---

目前绝大多数浏览器已原生支持 Base-64 的编码解码（最低支持到 IE10），详细的浏览器兼容性列表请参考[这里](https://developer.mozilla.org/zh-CN/docs/Web/API/WindowBase64/btoa#%E6%B5%8F%E8%A7%88%E5%99%A8%E5%85%BC%E5%AE%B9%E6%80%A7)。

## 使用场景

最常见的是对 Image 对象进行 Base-64 的编码解码。其次，我们也可以用于传输数据时的 Base-64 编码解码，比如浏览器地址栏中的参数可能在传输数据过程中出现的问题。

## 前置知识

- `window.btoa()`
- `window.atob()`

<!-- more -->

`window.btoa()` 从 `String` 对象中创建一个 Base-64 编码的 ASCII 字符串，其中字符串中的每个字符都被视为一个二进制数据字节。`atob()` 方法可以将通过 `window.btoa()` 编码的字符串数据解码。

## 语法

```javascript window.btoa()
const encodedData = window.btoa(stringToEncode); // 对数据进行编码
```

```javascript window.atob()
const decodedData = window.atob(encodedData); // 对数据进行解码
```

### 参数

- `window.btoa(stringToEncode)`

  `stringToEncode` - 一个字符串，其字符分别表示要编码为 ASCII 的二进制数据的单个字节。

- `window.atob(encodedData)`

  `encodedData` - 一个 Base-64 表示的字符串。

### 返回值

- `window.btoa(stringToEncode)`

  一个包含 `stringToEncode` 的 Base-64 表示的字符串。

- `window.atob(encodedData)`

  对 Base-64 字符串解码后的 `String` 对象。

## Unicode 字符串

在多数浏览器中，使用 `btoa()` 对 Unicode 字符串进行编码都会触发 `InvalidCharacterError` 异常。可以考虑下面这个例子，代码来自[Johan Sundström](http://ecmanaut.blogspot.com/2006/07/encoding-decoding-utf8-in-javascript.html)。

```javascript
// ucs-2 string to base64 encoded ascii
function utoa(str) {
  return window.btoa(unescape(encodeURIComponent(str)));
}
// base64 encoded ascii to ucs-2 string
function atou(str) {
  return decodeURIComponent(escape(window.atob(str)));
}
// Usage:
utoa("✓ à la mode"); // 4pyTIMOgIGxhIG1vZGU=
atou("4pyTIMOgIGxhIG1vZGU="); // "✓ à la mode"

utoa("I \u2661 Unicode!"); // SSDimaEgVW5pY29kZSE=
atou("SSDimaEgVW5pY29kZSE="); // "I ♡ Unicode!"
```

## 参考

- [浏览器原生支持 JS Base64 编码解码](https://blog.csdn.net/sunwork888/article/details/89436947?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_title-6&spm=1001.2101.3001.4242)
- [WindowOrWorkerGlobalScope.btoa()](https://developer.mozilla.org/zh-CN/docs/Web/API/WindowBase64/btoa)
- [WindowOrWorkerGlobalScope.atob()](https://developer.mozilla.org/zh-CN/docs/Web/API/WindowBase64/atob)
