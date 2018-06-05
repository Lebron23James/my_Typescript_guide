> ## 简介

**类型推论**：类型是在哪里如何被推断的。

---

## 1、基础

TypeScript 中没有明确指出类型的地方，类型推论会帮助提供类型。

```js
let x = 3;    //变量x 的类型被推断为数字
```

这种推断发生在 初始化变量和成员，设置默认参数值和决定函数返回值时。

## 2、最佳通用类型

计算最佳通用类型，会考虑所有的候选类型， 并给出一个兼容所有候选类型的类型。

```js
let x = [0, 1, null];
```

如上，为了推断 x 的类型， 这里有两种候选类型： number 和 null 。

--

最终通用类型取自候选类型，但是有些时候 候选类型共享相同的通用类型，但是却没有一个类型为所有候选类型的类型。

```js
let zoo = [new Rhino(), new Elephant(), new Snake()];
```

我们想让zoo 推断为animal 类型 但是这个数组里面没有对象是Animal 类型的，因此不能推断出这个结果。

没有找到最佳通用类型，类型推断的结果为**联合数组类型**： --   \(Rhino \| Elephant \| Snake\)\[\]

--

**注意**： 当候选类型不能使用的时候，我们需要明确的指出类型（Animal\[\] ）。

```js
let zoo: Animal[] = [new Rhino(), new Elephant(), new Snake()];
```

## 3、上下文类型

TypeScript 类型推论也可按照相反的方向进行。 当表达式的类型与所处的位置相关的时候，叫做“按上下文归类”。

```js
window.onmousedown = function(mouseEvent) {
    console.log(mouseEvent.button);  //<- Error
};
```

如上例：会得到一个类型错误。TypeScript 类型检查器使用 window.onmousedown 函数的类型来推断右边函数表达式的类型。

--

若上下文类型表达式包含了明确的类型信息（有明确的参数类型注解），上下文的类型被忽略。就不会报错了。

```js
window.onmousedown = function(mouseEvent: any) {
    console.log(mouseEvent.button);  //<- Now, no error is given
};
```

--

上下文类型会在很多情况下使用到：

* 函数的参数
* 赋值表达式的右边
* 类型断言
* 对象成员 和 数组字面量
* 返回值语句

上下文类型也会作为最佳通用类型的候选类型。

```js
function createZoo(): Animal[] {
    return [new Rhino(), new Elephant(), new Snake()];
}
```

以上最佳通用类型有四个候选者： Animal、Rhino、Elephant、Snake； 其中Animal会被作为最佳通用类型。

