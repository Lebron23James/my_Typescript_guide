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

#### （1）为函数定义类型；

我们可以给每个函数参数添加类型，也可以为函数本身添加返回值类型。

> TypeScript 能够根据返回语句 自动推断出返回值类型，因此我们通常省略函数返回值类型。

```js
function add(x: number, y: number): number {
    return x + y;
}

let myAdd = function(x: number, y: number): number { return x + y; };
```

#### （2）书写完整函数类型；

函数类型包含两部分：**参数类型** 和 **返回值类型**。    完整函数类型 这两部分是必须的。

```js
let myAdd: (x: number, y: number) => number =
    function(x: number, y: number): number { return x + y; };
```

> 以参数列表的形式写出**参数类型**，为每个参数指定一个名字和类型。
>
> 只要参数类型是匹配的，就认为它是有效的函数类型，不在乎参数名是否正确。

```js
let myAdd: (baseValue: number, increment: number) => number =
    function(x: number, y: number): number { return x + y; };
```

> 对于**返回值类型**：我们在函数 和 返回值类型之前使用\(`=>`\)符号。
>
> 返回值类型是函数类型的必要部分，没有任何返回值必须指定返回值类型为 `void` 而不能留空。

#### （3）推断类型；

如果你在赋值语句一边指定了类型，另一边没有的话； TypeScript编译器就会自动识别出类型。

这叫“按上下文归类”，是类型推论的一种。

```js
// myAdd has the full function type
let myAdd = function(x: number, y: number): number { return x + y; };

// The parameters `x` and `y` have the type number
let myAdd: (baseValue: number, increment: number) => number =
    function(x, y) { return x + y; };
```



