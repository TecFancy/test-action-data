---
title: 理解 JavaScript 中的对象
tags: JavaScript 中的对象、类和面向对象编程
categories: JavaScript
date: 2021-01-17 22:00:43
---

通常，通过创建 `Object` 的一个实例来创建自定义对象，然后再给它添加属性和方法。例如：

```js
const person = new Object();
person.name = "Olive";
person.age = 18;
person.gender = "female";
person.job = "Frontend Engineer";
person.sayName = function () {
  console.log(this.name);
};
```

上面这个例子创建了一个 `person` 对象，其中有四个属性（`name`、`age`、`gender` 和 `job`）和一个方法（`sayName()`）。`sayName()` 方法会在控制台打印 `this.name` 的值，这个属性会解析为 `person.name`。

除上面通过实例化 `Object` 的方式创建对象外，还可以通过对象字面量的方式创建 `person` 对象。例如：

```js
const person = {
  name: "Olive",
  age: 18,
  gender: "female",
  job: "Frontend Engineer",
  sayName() {
    console.log(this.name);
  },
};
```

这个例子跟前面例子中的 `person` 对象是等价的，他们的属性和方法都一样。

<!-- more -->

## 属性的类型

属性分两种：**数据属性**和**访问器属性**。

### 数据属性

数据属性包含一个保存数据值的位置。值会从这个位置读取，也会写入到这个位置。数据属性包含 4 个特性描述它们的行为。

- `[[Configurable]]`：表示属性是否可以通过 `delete` 删除属性，是否可以修改它的特性，以及是否可以把它改为访问器属性。默认情况下，所有直接定义在对象上的属性的这个特性都是 `true`。例如：

  ```js
  const person = {
    name: "Olive",
    age: 18,
    gender: "female",
    job: "Frontend Engineer",
    sayName() {
      console.log(this.name);
    },
  };
  Object.defineProperty(person, "name", {
    configurable: false,
  });
  delete person.name;
  console.log(person.name); // 'Olive'
  ```

  修改了 `[[Configurable]]` 属性值为 `false` 后，就意味着不能通过 `delete` 删除对象上的属性。而 `[[Configurable]]` 属性值为 `true` 时，若删除了 `person` 对象上的 `name` 属性后，`person.name` 的值应为 `undefined`。

  此外，对象上的一个属性被定义为不可配置后，就不能再变回可配置的了。再次调用 `Object.defineProperty()` 并修改任何非 `writable` 属性会导致错误。例如：

  ```js
  const person = {
    name: "Olive",
    age: 18,
    gender: "female",
    job: "Frontend Engineer",
    sayName() {
      console.log(this.name);
    },
  };
  Object.defineProperty(person, "name", {
    configurable: false,
  });
  Object.defineProperty(person, "name", {
    configurable: true,
  });
  /**
   * 这里会报错：
   * Uncaught TypeError: Cannot redefine property: name
   */
  ```

  因此，虽然可以对对象上的同一个属性多次调用 `Object.defineProperty()`，但当把 `configurable` 设置为 `false` 后就会受到限制。

- `[[Enumerable]]`：表示属性是否可以通过 `for-in` 循环返回。默认情况下，所有直接定义在对象上的属性的这个特性都是 `true`。以 `person` 对象为例：

  ```js
  for (const key in person) {
    console.log(key);
  }
  ```

  上面这段代码通过 `for-in` 语句对 `person` 对象执行的遍历操作，会在控制台依次打印出如下结果：

  ```js
  /**
   * "name"
   * "age"
   * "gender"
   * "job"
   * "sayName"
   */
  ```

  当把 `person` 对象上的 `name` 属性的 `enumerable` 属性设置为 `false` 后，再使用 `for-in` 语句执行遍历操作时，控制台将不会打印出 `name` 属性。

- `[[Writable]]`：表示属性的值是否可以被修改。默认情况下，所有直接定义在对象上的属性的这个特性都是 `true`。例如：

  ```js
  person.name = "Jack";
  console.log(person.name); // "Jack"
  ```

  当对 `person.name` 重新赋值为 `Jack` 后，在控制台打印出的值为 `Jack`。若将 `person.name` 属性上的 `writable` 设置为 `false`，则不能对 `person.name` 属性执行重新赋值的操作。例如：

  ```js
  const person = {
    name: "Olive",
    age: 18,
    gender: "female",
    job: "Frontend Engineer",
    sayName() {
      console.log(this.name);
    },
  };
  Object.defineProperty(person, "name", {
    writable: false,
  });
  person.name = "Jack";
  console.log(person.name); // "Olive"
  ```

  因把 `name` 属性的 `writable` 值设置为 `false`，当再次尝试修改 `person.name` 的值时，我们发现并没有生效，控制台中打印出的 `person.name` 的值依然是通过对象字面量定义时的值——`Olive`。

- `[[Value]]`：包含属性实际的值。默认情况下，所有直接定义在对象上的属性的这个特性都是 `true`。例如：

  ```js
  const person = {
    name: "Olive",
    age: 18,
    gender: "female",
    job: "Frontend Engineer",
    sayName() {
      console.log(this.name);
    },
  };
  Object.defineProperty(person, "name", {
    value: "Jack",
  });
  console.log(person.name); // "Jack"
  ```

  可以看到 `person.name` 属性值已被修改为 `Jack`。

### 访问器属性

访问器属性不包含数据值。它包含一个获取（getter）函数和一个设置（setter）函数，不过这两个函数不是必需的。在读取访问器属性时，会调用获取函数，该函数会返回一个有效值。在写入访问器属性时，会调用设置函数并传入新的值，该函数必须决定对数据做出什么修改。访问器属性有 4 个特性描述他们的行为。

- `[[Configurable]]`：表示属性是否可以通过 `delete` 删除并重新定义，是否可以修改它的特性，以及是否可以把它改为数据属性。默认情况下，所有直接定义在对象上的属性的这个特性都是 `true`。
- `[[Enumerable]]`：表示属性是否可以通过 `for-in` 循环返回。默认情况下，所有直接定义在对象上的属性的这个特性都是 `true`。
- `[[Get]]`：获取函数，在读取属性时调用。默认值为 `undefined`。
- `[[Set]]`：设置函数，在设置属性时调用。默认值为 `undefined`。

访问器属性不可直接定义，需通过 `Object.defineProperty()` 实现。例如：

```js
// 定义一个对象，包含伪私有成员 `year_` 和公共成员 `edition`
const book = {
  year_: 2017,
  edition: 1,
};
Object.defineProperty(book, "year", {
  get() {
    return this.year_;
  },
  set(newValue) {
    if (newValue > 2017) {
      this.year_ = newValue;
      this.edition += newValue - 2017;
    }
  },
});
book.year = 2018;
console.log(book.edition); // 2
```

在这个例子中，`book` 对象有 2 个属性：`year_` 和 `edition`。`year_` 中的下划线通常表示不希望该属性在对象外部被访问。

另一个参数 `year` 被定义为一个访问器属性，其中获取函数（`get()`）简单地返回 `year_` 地值，而设置函数（`set()`）会根据传入的参数 `newValue` 做一些简单的判断来计算正确的 `edition` 值，同时把传入的参数赋值给私有属性 `year_`。

因此，把 `year` 属性修改为 2018 后会导致属性 `year_` 的值变为 2018，同时 `edition` 的值变为 2。这就是访问器属性的典型使用场景，即设置一个值会导致另外一些值发生变化。

这里要注意，在不支持 `Object.defineProperty()` 的浏览器中无法修改 `[[Configurable]]` 或 `[[Enumerable]]`。

## 定义多个属性

ECMAScript 提供了 `Object.defineProperties()` 方法允许在一个对象上同时定义多个属性。该方法可以通过多个描述符一次性定义多个属性。

它接收 2 个参数：要添加或修改属性的对象和另一个描述符对象，其属性与要添加或修改的属性一一对应。例如：

```js
let book = {};
Object.defineProperties(book, {
  year_: {
    value: 2017,
  },
  edition: {
    value: 1,
  },
  year: {
    get() {
      return this.year_;
    },
    set(newValue) {
      if (newValue > 2017) {
        this.year_ = newValue;
        this.edition += newValue - 2017;
      }
    },
  },
});
```

这段代码在 `book` 对象上定义了 2 个数据属性：`year_` 和 `edition`，和 1 个访问器属性 `year`。该对象与之前的 `book` 对象是一样的。

唯一取表就是所有属性是同时定义的，并且数据属性的 `configurable`、`enumerable` 和 `writable` 特性值都为 `false`。因此，当给访问器属性 `year` 重新赋值并不会生效。想要对访问器属性 `year` 重新赋值后仍能更新私有成员 `year_` 和 `edition` 属性，应将对其属性对应的 `writable` 特性设置为 `true`。例如：

```js
let book = {};
Object.defineProperties(book, {
  year_: {
    value: 2017,
    writable: true,
  },
  edition: {
    value: 1,
    writable: true,
  },
  year: {
    get() {
      return this.year_;
    },
    set(newValue) {
      if (newValue > 2017) {
        this.year_ = newValue;
        this.edition += newValue - 2017;
      }
    },
  },
});
book.year = 2020;
console.log(book.edition); // 4
```

## 读取属性的特性

使用 `Object.getOwnPropertyDescriptor()` 方法可以获取指定对象属性的属性描述符。该方法接收 2 个参数：属性所在的对象和要取得其描述符的属性名。返回值是一个对象，对于数据属性包含 `configurable`、`enumerable`、`writable` 和 `value` 属性。例如：

```js
const book = {};
Object.defineProperties(book, {
  year_: {
    value: 2017,
  },
  edition: {
    value: 1,
  },
  year: {
    get: function () {
      return this.year_;
    },
    set: function (newValue) {
      if (newValue > 2017) {
        this.year_ = newValue;
        this.edition += newValue - 2017;
      }
    },
  },
});

const descriptorOfPrivateYear = Object.getOwnPropertyDescriptor(book, "year_");
console.log(descriptor.value); // 2017
console.log(descriptor.configurable); // false
console.log(typeof descriptor.get); // "undefined"

const descriptorOfPublicYear = Object.getOwnPropertyDescriptor(book, "year");
console.log(descriptor.value); // undefined
console.log(descriptor.enumerable); // false
console.log(typeof descriptor.get); // "function"
```

上面这个例子中：对于数据属性 `year_`，`value` 等于原来的值，`configurable`、`enumerable` 和 `writable` 是 `false`，`get` 和 `set` 方法是 `undefined`。对于访问器属性 `year`，`value` 是 `undefined`，`configurable` 和 `enumerable` 是 `false`，`get` 是一个指向获取函数的指针，`set` 是一个指向设置函数的指针。

静态方法 `Object.getOwnPropertyDescriptors()` ，会在对象的每个自有属性上调用 `Object.getOwnPropertyDescriptor()` 并在一个新对象中返回它们。在上面例子中使用这个静态方法，会返回如下对象：

```js
const book = {};
Object.defineProperties(book, {
  year_: {
    value: 2017,
  },
  edition: {
    value: 1,
  },
  year: {
    get: function () {
      return this.year_;
    },
    set: function (newValue) {
      if (newValue > 2017) {
        this.year_ = newValue;
        this.edition += newValue - 2017;
      }
    },
  },
});

console.log(Object.getOwnPropertyDescriptors(book));
// {
//   year_: {
//     value: 2017,
//     writable: false,
//     enumerable: false,
//     configurable: false
//   },
//   edition: { value: 1, writable: false, enumerable: false, configurable: false },
//   year: {
//     get: [Function: get],
//     set: [Function: set],
//     enumerable: false,
//     configurable: false
//   }
// }
```

## 合并对象

使用 `Object.assign()` 方法可以合并对象。该方法接收一个目标对象和一个或多个源对象作为参数，然后将每个源对象中可枚举和自有属性复制到目标对象。对每个符合条件的属性，这个方法会使用源对象上的 `[[Get]]` 获取其属性的值，使用目标对象的 `[[Set]]` 设置属性的值。例如：

```js
/**
 * 简单复制
 */
let dest = {};
let src = { id: "src" };
let result = {};

result = Object.assign(dest, src);

// Object.assign() 修改目标对象
// 也会返回修改后的对象
console.log(dest === result); // true
console.log(dest !== src); // true
console.log(result); // { id: 'src' }
console.log(dest); // { id: 'src' }

/**
 * 多个源对象
 */
dest = {};
result = Object.assign(dest, { name: "Olive", age: 18 });
console.log(result); // { name: 'Olive', age: 18 }

/**
 * 获取函数与设置函数
 */
dest = {
  set name(val) {
    console.log(`Invoked dest setter with param ${val}`);
  },
};
src = {
  get name() {
    console.log("Invoked src getter");
    return "Olive";
  },
};
Object.assign(dest, src);
// 调用 src 的获取方法
// 调用 dest 的设置方法并传入参数 `Olive`
// 因为这里的设置函数没有执行赋值操作
// 所以实际上并没有把值转移过来
console.log(dest);
// 'Invoked src getter
// 'Invoked dest setter with param foo'
// { name: [Setter] }
```

`Object.assign()` 方法实际上对每个源对象执行的是浅复制操作。如果多个源对象用于相同的属性，则使用最后一个赋值的值。

另外，从源对象[访问器属性](./#访问器属性)取得的值，比如获取函数，会作为一个静态值赋给目标对象。也就是说，不能在两个对象间转移获取函数和设置函数。

```js
let dest, src, result;

/**
 * 覆盖属性
 */
dest = { id: "dest" };

result = Object.assign(
  dest,
  { id: "src1", name: "Olive" },
  { id: "src2", age: 18 }
);

// Object.assign 会覆盖重复的属性
console.log(result); // { id: 'src2', name: 'Olive', age: 18 }

// 可以通过目标对象上的设置函数观察到覆盖过程：
dest = {
  set id(value) {
    console.log(value);
  },
};

Object.assign(dest, { id: "first" }, { id: "second" }, { id: "third" });
// 'first'
// 'second'
// 'third'

/**
 * 对象引用
 */
dest = {};
src = {
  name: {},
};

Object.assign(dest, src);
// 潜复制意味着只会复制对象的引用
console.log(dest); // { name: {} }
console.log(dest.name === src.name); // true
```

## 对象标识和相等判断

有些特殊情况下，相等操作符 `===` 并不能符合我们的预期：

```js
// 这些是 `===` 符合预期的情况
console.log(true === 1); // false
console.log({} === {}); // false
console.log("2" === 2); // false

// 下面的情况在不同的 js 引擎中表现不同
console.log(+0 === -0); // true
console.log(+0 === 0); // true
console.log(-0 === 0); // true

// 要确定 NaN 的相等性，必须要使用 `isNaN()`
console.log(NaN === NaN); // false
console.log(isNaN(NaN)); // true
```

为改善上述情况，ES6 规范新增了 `Object.is()` 方法，该方法接收 2 个参数：

```js
console.log(Object.is(true, 1)); // false
console.log(Object.is({}, {})); // false
console.log(Object.is("2", 2)); // false

// 正确的 0、+0、-0 相等/不相等判定
console.log(Object.is(+0, -0)); // false
console.log(Object.is(+0, 0)); // true
console.log(Object.is(-0, 0)); // false

// 正确的 `NaN` 相等性判定
console.log(Object.is(NaN, NaN)); // true
```

要检查超过 2 个值，递归地利用相等性判断即可：

```js
function recursivelyCheckEqual(x, ...rest) {
  return (
    Object.is(x, rest[0]) && (rest.length < 2 || recursivelyCheckEqual(...rest))
  );
}

const first = "Olive";
const second = "Olive";
const third = "Olive";
const fourth = "Olive";

const result = recursivelyCheckEqual(first, second, third, fourth);
console.log(result); // true
```
