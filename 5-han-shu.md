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

## 3、可选参数 和 默认参数

#### （1）可选参数

JavaScript里，每个函数参数都是可选的，可传可不传。 没传参的时候，它的值就是undefined。

TypeScript中，每个函数参数都是必须的；即传递给一个函数参数的参数个数必须与函数期望的参数个数一致。

```js
function buildName(firstName: string, lastName: string) {
    return firstName + " " + lastName;
}

let result1 = buildName("Bob");                  // error, too few parameters
let result2 = buildName("Bob", "Adams", "Sr.");  // error, too many parameters
let result3 = buildName("Bob", "Adams");         // ah, just right
```

在TypeScript里，我们可以在参数名旁使用`?`实现可选参数的功能。

```js
function buildName(firstName: string, lastName?: string) {
    if (lastName)
        return firstName + " " + lastName;
    else
        return firstName;
}

let result1 = buildName("Bob");  // works correctly now
let result2 = buildName("Bob", "Adams", "Sr.");  // error, too many parameters
let result3 = buildName("Bob", "Adams");  // ah, just right
```

**注意**：可选参数必须跟在必须参数后面。



#### （2）默认参数

在TypeScript 中，我们可以给参数指定一个默认值--- 当用户没有传递这个参数 或 传递的值时undefined 的时候生效。

```js
function buildName(firstName: string, lastName = "Smith") {
    return firstName + " " + lastName;
}

let result1 = buildName("Bob");                  // works correctly now, returns "Bob Smith"
let result2 = buildName("Bob", undefined);       // still works, also returns "Bob Smith"
let result3 = buildName("Bob", "Adams", "Sr.");  // error, too many parameters
let result4 = buildName("Bob", "Adams");         // ah, just right
```

**注意**：默认参数不需要跟在必须参数后面。

> 如果带默认值的参数出现在必须参数前面，用户必须明确的传入`undefined`值来获得默认值。

```js
function buildName(firstName = "Will", lastName: string) {
    return firstName + " " + lastName;
}

let result1 = buildName("Bob");                  // error,必须传入undefined 来占位
let result2 = buildName("Bob", "Adams", "Sr.");  // error, 参数过多
let result3 = buildName("Bob", "Adams");         // okay and returns "Bob Adams"
let result4 = buildName(undefined, "Adams");     // okay and returns "Will Adams"
```

## 4、剩余参数

必要参数、默认参数、可选参数的共同点：他们表示某一个参数。

有时你想同时操作多个参数、或者你并不知道有多少个参数会传进来。【JavaScript中用arguments来访问传入的参数。】

TypeScript 中，你可以用 把左右参数收集到一个变量：

```js
function buildName(firstName: string, ...restOfName: string[]) {
  return firstName + " " + restOfName.join(" ");
}

let employeeName = buildName("Joseph", "Samuel", "Lucas", "MacKinzie");
```

剩余参数 被当做个数不限的可选参数【一个都没有 或者 有任意个】。

编译器会创建一个参数数组，数组名就是（...）后面的名字，可在函数体内使用这个数组。

> （...）也可以在带有剩余参数的函数类型定义上使用

```
function buildName(firstName: string, ...restOfName: string[]) {
  return firstName + " " + restOfName.join(" ");
}

let buildNameFun: (fname: string, ...rest: string[]) => string = buildName;
```



