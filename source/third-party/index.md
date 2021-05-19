---
title: 第三方库
date: 2020-12-16 11:08:00
description: 汇总有用的第三方库
comments: false
---

## [uuid](https://www.npmjs.com/package/uuid)

这是一款创建 UUID 的第三方库。

### 安装

``` bash
npm i uuid # or
yarn add uuid
```

### 使用方法

``` js
// ES6 module syntax
import { v4 as uuidv4 } from 'uuid';
uuidv4(); // ⇨ '9b1deb4d-3b7d-4bad-9bdd-2b0d7b3dcb6d'

// using CommonJS syntax
const { v4: uuidv4 } = require('uuid');
uuidv4(); // ⇨ '1b9d6bcd-bbfd-4b2d-9b5d-ab8dfbbd4bed'
```

官方文档参考[这里](https://github.com/uuidjs/uuid#readme)。
