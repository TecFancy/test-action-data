---
title: React Hook 之封装 useMount 自定义 hook
categories: React
tags:
  - React Hooks
  - React 钩子函数
  - React 自定义 Hook
date: 2021-02-22 13:49:34
---

下面是一个使用 React 编写的函数式组件：

```jsx
/**
 * user-list.jsx
 */

import React, { useState, useEffect } from 'react';

const UserList = () => {
  const [users, setUsers] = useState([]);
  useEffect(() => {
    fetch('/users').then(async response => {
      if (response.ok) {
        setUsers(await response.json());
      } else {
        // do something...
      }
    });
  }, []);

  return (
    <ul>
      {users ? users.map((user) => (
         <li key={user.id}>{user.name}</li>
       )) : null}
    <ul/>
  );
};

export default UserList;
```

<!-- more -->

当组件 `UserList` 初次加载时，调用了一次获取用户数据的接口，该接口的返回值用来渲染页面上的用户列表。`useEffect` 函数的第二个参数就表示这个 hook 在组件渲染到页面后只会调用一次，可以编写一个自定义 hook 将这种只执行一次的 effect 抽象成自定义 hook。

```jsx
/**
 * use-mount.jsx
 */

import React, { useEffect } from "react";

const useMount = (callback) => {
  useEffect(() => {
    if (callback && typeof callback === "function") {
      callback();
    }
  }, []);
};

export default useMount;
```

调用 `useMount` 自定义 hook 时传入一个函数作为参数，`useMount` 会去执行这个回调函数，并且只会在组件渲染到页面后只执行一次。使用方法：

```jsx
/**
 * user-list.jsx
 */

import React, { useState, useEffect } from 'react';
import useMount from 'use-mount';

const UserList = () => {
  const [users, setUsers] = useState([]);
  useMount(() => {
    fetch('/users').then(async response => {
      if (response.ok) {
        setUsers(await response.json());
      } else {
        // do something...
      }
    });
  });

  return (
    <ul>
      {users ? users.map((user) => (
         <li key={user.id}>{user.name}</li>
       )) : null}
    <ul/>
  );
};

export default UserList;
```

可以发现，和直接使用 `useEffect` 很相似，区别是使用 `useMount` 自定义 hook 时省略了 `useEffect` hook 的第二个参数空数组。之后就可以把只在组件加载后执行一次的副作用操作都写在 `useMount` 自定义 hook 中了。
