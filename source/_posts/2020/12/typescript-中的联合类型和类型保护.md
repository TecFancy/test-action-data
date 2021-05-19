---
title: typescript 中的联合类型和类型保护
tags: TypeScript
date: 2020-12-31 22:36:14
---

下面通过一个简单的例子来说明 typescript 中的联合类型。

先来两只小动物：Bird 和 Dog。我们都知道，小鸟会叫、会飞，小狗会叫但是不会飞，根据它们的这两种属性我们就能定义两个接口，如下：

```ts
interface Bird {
  fly: boolean;
  sing: () => {}; // 小鸟唱歌
}

interface Dog {
  fly: boolean;
  bark: () => {}; // 小狗汪汪叫
}
```

<!-- more -->

定义好了接口，那我们就来训练一下这两只小动物吧。下面我们定义一个函数 `trainAnimal`，该函数接收一个形参 `animal`，如果这个参数可以指 `Bird` 也可以指 `Dog`，那么我们可以这样写：

```ts
function(animal: Bird | Dog) {
  // do something...
}
```

像上面函数中使用 `|` 将接口名分割开的写法就是 typescript 中联合类型的写法，联合类型允许我们将多个类型组合起来。在这个列子中 `animal` 可以是 `Bird` 和 `Dog` 两种类型中的任意一种。就像现实世界中我们所说的动物，小鸟和小狗都属于动物的一种。这样理解联合类型是不是就清楚多了呢？

类型 `Bird` 和 `Dog` 中都有 `fly` 属性，所以我们能很方便的访问到它们共有的这个属性 `fly`，但是其中的 `sing` 方法或 `bark` 方法就不能直接访问了。因为 `animal` 可能是 `Bird` 也可能是 `Dog`，如果 `animal` 是 `Bird` 的话肯定没有 `bark` 方法，如果 `animal` 是 `Dog` 的话，那肯定就没有 `sing` 方法了。那么我们该如何断定 `animal` 属于哪种类型呢？

下面有请**类型断言**上场。这家伙有个特点：我说是啥就是啥！不是也得是！

类型断言说：如果你会飞，你就是小鸟；如果不会飞，那你就是小狗！

```ts
function(animal: Bird | Dog) {
  if (animal.fly) {
    (animal as Bird).sing();
  } else {
    (animal as Dog).bark();
  }
}
```

注意到 `as` 语法了吗？通常我们在 `as` 后接一个类型名，就表示 `as` 前的变量属于 `as` 后的类型，然后再以小括号包围起来。这个例子中，如果 `animal` 中 `fly` 属性为真的话，`animal` 指的就是 `Bird` 类型，然后就能访问到 `Bird` 中的 `sing` 方法了；反之，`animal` 就是 `Dog` 类型，这样就能访问到 `Dog` 类型中的 `bark` 方法了。

上面提到的**类型断言**是类型保护的一种方法，除此之外还有许多其他方式也可以实现类型保护的能力。比如使用 `in` 语法。

`in` 语法要比上面的类型断言方式简单多了，不用我们额外判断方法传入的参数中是否存在 `fly` 属性。

```ts
function trainAnimal(animal: Bird | Dog) {
  if ("sing" in animal) animal.sing();
  else animal.bark();
}
```

上面代码的意思是，我们利用 `in` 语法直接判断 `animal` 属性中是否存在 `sing` 属性，如果存在就直接调用 `sing` 方法。因为 `animal` 属性除了是 `Bird` 类型外，只可能是 `Dog` 类型，所以我们可以在 `if` 语句的 `else` 分支中直接调用 `bark` 方法。

通过这个简单的例子，我们就知道什么是联合类型和类型保护了吧。
