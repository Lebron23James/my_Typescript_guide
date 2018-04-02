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
    Up = "UP",
    Down = "DOWN",
    Left = "LEFT",
    Right = "RIGHT",
}
```

由于字符串枚举没有自增长的行为，字符串枚举可以很好的序列化。

字符串枚举允许你提供一个运行时有意义的并且可读的值，独立于枚举成员的名字。















