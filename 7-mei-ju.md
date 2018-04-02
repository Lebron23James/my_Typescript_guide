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



