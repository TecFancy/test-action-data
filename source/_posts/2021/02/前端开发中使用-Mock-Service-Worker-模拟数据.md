---
title: 前端开发中使用 Mock Service Worker 模拟数据
categories: Mock Server
tags:
  - Mock Service Worker
  - msw
date: 2021-02-26 16:26:32
---

## 安装

在项目根目录执行以下命令：

```bash
$ npm install msw --save-dev
# or
$ yarn add msw --dev
```

<!-- more -->

## 定义 mocks（模拟 REST API）

使用 request handler 来定义要模拟的请求。

1. 创建 `src/mocks` 目录：

   ```bash
   $ mkdir src/mocks
   ```

2. 创建 `src/mocks/handler.js` 文件：

   ```bash
   $ touch src/mocks/handlers.js
   ```

3. 从 `msw` 导入 `rest` :

   ```js
   // src/mocks/handlers.js
   import { rest } from "msw";
   ```

以下面两个 API 为例：

- `POST /login`，允许用户登录。
- `GET /user`，返回登录用户的信息。

```js
// src/mocks/handlers.js
import { rest } from "msw";

export const handlers = [
  // Handles a POST /login request
  rest.post("/login", null),

  // Handles a GET /user request
  rest.get("/user", null),
];
```

要模拟返回值，需要在对应的 `post` 或 `get` 方法中传入 response resolver。response resolver 函数接受 3 个参数：

- `req`：匹配的请求信息。
- `res`：用于模拟返回值的方法集。
- `ctx`：设置 `status code`、`headers`、`body`等。

```js
// src/mocks/handlers.js
import { rest } from "msw";

export const handlers = [
  rest.post("/login", (req, res, ctx) => {
    // Persist user's authentication in the session
    sessionStorage.setItem("is-authenticated", "true");

    return res(
      // Respond with a 200 status code
      ctx.status(200)
    );
  }),

  rest.get("/user", (req, res, ctx) => {
    // Check if the user is authenticated in this session
    const isAuthenticated = sessionStorage.getItem("is-authenticated");

    if (!isAuthenticated) {
      // If not authenticated, respond with a 403 error
      return res(
        ctx.status(403),
        ctx.json({
          errorMessage: "Not authorized",
        })
      );
    }

    // If authenticated, return a mocked user details
    return res(
      ctx.status(200),
      ctx.json({
        username: "admin",
      })
    );
  }),
];
```

## 集成

上面的 `src/mocks/handlers.js` 文件在 Browser 和 Node 环境是通用的。因为 Service Worder 不支持在 Node 中运行，所以集成过程因环境而异。

### 集成到 Browser 环境

使用 Mock Service Worker CLI 工具在项目根目录执行：

```bash
$ npx msw init <PUBLIC_DIR> --save
```

将 `<PUBLIC_DIR>` 替换成你项目中的 public 目录。比如，使用的是 Create React App 创建的项目应执行：

```bash
$ npx msw init public/ --save
```

[如何确定 `public` 目录？](https://mswjs.io/docs/getting-started/integrate/browser#where-is-my-public-directory)

#### 配置 worker

- 在 `src/mocks` 目录下创建 `browser.js` 文件：

  ```bash
  $ touch src/mocks/browser.js
  ```

- 从 `msw` 导入 `setupWorker` 方法，把上面创建的 `src/mocks/handler.js` 文件中的 `handler` 也一并导入进来：

  ```js
  // src/mocks/browser.js
  import { setupWorker } from "msw";
  import { handlers } from "./handlers";

  // This configures a Service Worker with the given request handlers.
  export const worker = setupWorker(...handlers);
  ```

#### 启动 worker

Mock Service Worker 不应被用于生产环境，应在测试环境中使用：

```jsx
// src/index.js
import React from "react";
import ReactDOM from "react-dom";
import App from "./App";

if (process.env.NODE_ENV === "development") {
  const { worker } = require("./mocks/browser");
  worker.start();
}

ReactDOM.render(<App />, document.getElementById("root"));
```

#### 验证和检查

在浏览器控制台中应能看到打印出的成功的消息：

![验证和检查 msw](https://gitee.com/smpower/oss/raw/master/hi-ruofei.com/XGFC5j.png)

### 集成到 Node 环境

参阅 [这里](https://mswjs.io/docs/getting-started/integrate/node) 的文档说明，按照步骤可将 msw 集成到 Node 环境。

## 小结

Mock Service Worker 是一个非常强大的 mock server 库，可查看 [官网](https://mswjs.io/) 查看详细信息。
