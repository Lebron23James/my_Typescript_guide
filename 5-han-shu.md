> ## 简介

函数是JavaScript 应用程序的基础，可以实现抽象层、模拟类、信息隐藏 和 模块。

TypeScript 虽然已经支持类、命名空间 和 模块； 但是函数仍是主要定义** 行为** 的地方。

TypeScript 为 JavaScript 函数添加了额外的功能，让我们更容易的使用。

---

## 1、命名函数 和 匿名函数。

```js
// Named function
function add(x, y) {
    return x + y;
}

// Anonymous function
let myAdd = function(x, y) { return x + y + z; };

let z = 100;        // 函数可以 捕获 函数体外部的变量。
```

## 2、函数类型

（1）为函数定义类型；

我们可以给每个函数参数添加类型，也可以为函数本身添加返回值类型。

> TypeScript 能够根据返回语句 自动推断出返回值类型，因此我们通常省略函数返回值类型。

```js
function add(x: number, y: number): number {
    return x + y;
}

let myAdd = function(x: number, y: number): number { return x + y; };
```

（2）书写完整函数类型；

（3）推断类型；

