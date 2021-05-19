---
title: React Hook 之封装 useArray 自定义 hook
categories: React
tags:
  - React Hooks
  - React 钩子函数
  - React 自定义 Hook
date: 2021-02-22 17:23:17
---

在对数组操作时，我们经常要执行新增或删除数组元素的操作，将一些常用的数组操作封装成 React 自定义 hook 使用会非常方便。下面将封装一个自定义的 `useArray` React hook，实现数组的取值、设置值、添加、删除和清理数组的功能。

<!-- more -->

```jsx
/**
 * use-array.tsx
 */

import React, { useState } from "react";

/**
 * 封装 useArray 自定义 React hook
 * @param {Array} initialArray 要处理的数组
 * @returns {Array} Object.value 处理过后的数组元素
 * @returns {Function} Object.setValue 更新数组的方法，该方法是 `useState()` 方法返回的用于更新 state 的函数
 * @returns {Function} Object.add 向数组末尾添加元素的方法
 * @returns {Function} Object.clear 该方法会将传入的数组清空
 * @returns {Function} Object.removeIndex 该方法会将指定的数组元素从数组中移除
 */
export const useArray = (initialArray) => {
  const [value, setValue] = useState(initialArray);
  return {
    value,
    setValue,
    add: (item) => setValue([...value, item]),
    clear: () => setValue([]),
    removeIndex: (index) => {
      const copy = [...value];
      copy.splice(index, 1);
      setValue(copy);
    },
  };
};
```

上面封装的自定义 hook `useArray` 将返回一系列对处理数组有用的方法和值：

- `value`：处理过后的数组元素。
- `setValue`：该方法是 `useState()` 方法返回的用于更新数组的方法。
- `add`：该方法接受一个任意值，允许用户向数组末尾添加一个新元素。
- `clear`：该方法会把传入的数组中的元素清空。
- `removeIndex`：该方法接受一个数组元素的下标，会将数组中对应该下标的元素从数组中移除。

使用方法：

```jsx
import React from "react";
import useArray from "use-array";

const UserList = () => {
  // { value, setValue, add, clear, removeIndex } = useArray(initialArray);
  const { value, add } = useArray([
    { id: 1, name: "Olive" },
    { id: 2, name: "Jack" },
  ]);
  add({ id: 3, name: "Amy" });

  return <ul>{value ? <li key={value.id}>{value.name}</li> : null}</ul>;
};

export default UserList;
```

之后，根据业务场景可随时向 `useArray` 自定义 hook 中添加额外逻辑。一处添加，到处使用。
