> ## 简介

使用枚举可以定义一些带名字的常量。使用枚举可以清晰滴表达意图或创建一组有区别的用例。

TypeScript 支持**数字枚举** 和 **字符串枚举**。

---

## 1、数字枚举

我们定义一个数字枚举，其中up的值为0； dowm的值为1； 依次递增……这种自增结构。 且每个枚举成员的值是不同的。

```js
enum Direction {
    Up,
    Down,
    Left,
    Right,
}
```

我们还可以在定义的枚举中使用 初始化器；up初始化为1，其余成员从1 开始递增。

```js
enum Direction {
    Up = 1,
    Down,
    Left,
    Right
}
```

**枚举的使用**： 通过枚举的属性来访问枚举成员 && 枚举的名字访问枚举类型。

```js
enum Response {
    No = 0,
    Yes = 1,
}

function respond(recipient: string, message: Response): void {
    // ...
}

respond("Princess Caroline", Response.Yes)
```

不带初始化器的枚举放置的位置：**第一的位置** && **使用数字常量或者其它常量初始化了的枚举后面**。

以下情况是不被允许的：

```js
enum E {
    A = getSomeValue(),
    B, // error! 'A' is not constant-initialized, so 'B' needs an initializer
}
```

## 2、字符串枚举

在一个字符串枚举中，每个成员都必须用**字符串字面量** 或者** 另外一个字符串枚举成员**进行初始化。

```js
enum Direction {
    Down = "DOWN",
    Left = "LEFT",
    Right = "RIGHT",
}
```

由于字符串枚举没有自增长的行为，字符串枚举可以很好的序列化。

字符串枚举允许你提供一个运行时有意义的并且可读的值，独立于枚举成员的名字。

## 3、异构枚举

从技术的角度来说，枚举可以混合字符串和数字成员，除非你真的想利用JavaScript运行时的行为，否则不建议这么做。

```js
enum BooleanLikeHeterogeneousEnum {
    No = 0,
    Yes = "YES",
}
```

## 4、枚举成员类型

每个枚举成员都带有一个值，他可以是`常量`或者是 `需要计算得出的值`。

##### （1）满足以下条件时候，枚举成员被当做是常量。

> a 、 它是枚举的第一个成员，且没有初始化器，这种情况下被赋值为0；

```js
// E.X is constant:
enum E { X }
```

> b、 它不带有初始化器，并且它之前的枚举成员是一个数字常量；

```js
// All enum members in 'E1' and 'E2' are constant.

enum E1 { X, Y, Z }

enum E2 {
    A = 1, B, C
}
```

> c、 枚举成员使用** 常量枚举表达式** 初始化。

**常量枚举表达式**是TypeScript 的子集，它可以在编译阶段求值。当一个表达式满足下面条件之一的时候就是一个**常量枚举表达式**：

1. 一个枚举表达式字面量（主要是字符串字面量或数字字面量）
2. 一个对之前定义的常量枚举成员的引用（可以是在不同的枚举类型中定义的）
3. 带括号的常量枚举表达式
4. 一元运算符`+`,`-`,`~`其中之一应用在了常量枚举表达式

5. 常量枚举表达式做为二元运算符`+`,`-`,`*`,`/`,`%`,`<<`,`>>`,`>>>`,`&`,`|`,`^`的操作对象。 若常数枚举表达式求值后为`NaN`或`Infinity`，则会在编译阶段报错。

```js
enum FileAccess {
    // constant members
    None,
    Read    = 1 << 1,
    Write   = 1 << 2,
    ReadWrite  = Read | Write,
    // computed member
    G = "123".length
}
```

##### **（2）所有其它情况的枚举成员被当作是需要计算得出的值。**

```
   --
```

## 5、联合枚举与枚举成员的类型

存在一种特殊的非计算的常量枚举成员的子集，称为字面量枚举成员。

**字面量枚举成员**指的是：不带有初始值的常量枚举成员，或者初始值为以下一种；

1. 任何字符串字面量（例：“foo”  “bar”）
2. 任何数字字面量（例：3, 56）
3. 应用了一元 — 符号的数字字面量（例：-1，-20）

当所有的枚举成员都拥有了字面量枚举时，它就带有了一种特殊的语义。

##### （1）首先枚举成员成为了类型。（例如：我们可以说某些成员只能是枚举成员的值）

```js
enum ShapeKind {
    Circle,
    Square,
}

interface Circle {
    kind: ShapeKind.Circle;
    radius: number;
}

interface Square {
    kind: ShapeKind.Square;
    sideLength: number;
}

let c: Circle = {
    kind: ShapeKind.Square,
    //    ~~~~~~~~~~~~~~~~ Error!
    radius: 100,
}
```

##### （2）枚举类型本身变成了每个枚举成员的 **联合**。

```js
enum E {
    Foo,
    Bar,
}

function f(x: E) {
    if (x !== E.Foo || x !== E.Bar) {
        //             ~~~~~~~~~~~
        // Error! Operator '!==' cannot be applied to types 'E.Foo' and 'E.Bar'.
    }
}
```

这个例子里，我们先检查`x`是否不是`E.Foo`。 如果通过了这个检查，然后`||`会发生短路效果，`if`句体里的内容会被执行。

然而，这个检查没有通过，那么`x`则_只能_为`E.Foo`，因此没理由再去检查它是否为`E.Bar`。

## 6、运行时的枚举

枚举是在运行时真正存在的对象！！

> **创建一个以属性名作为对象成员的 对象！！**

```js
enum E {
    X, Y, Z
}
// 可以传递给函数
function f(obj: { X: number }) {
    return obj.X;
}

// Works, since 'E' has a property named 'X' which is a number.
f(E);
```

## 7、反向映射

数字枚举成员还具有反向映射： 从枚举值到枚举名字！！

```js
enum Enum {
    A
}
let a = Enum.A;
let nameOfA = Enum[A];  // a

```



