> ## 简介

**泛型** 可以用来创建可重用的组件，一个组件可以支持多种类型的数据。这样用户就可以以自己的数据类型来使用组件。、

---

## 1、泛型 -- hello world

使用`any` 类型来定义函数可以接受任何类型的`arg`参数。但是这样会丢失一些信息：传入的类型与返回的类型应该是相同的。

```js
function identity(arg: any): any {
    return arg;
}
```

因此需要一种方法使返回值的类型与传入参数的类型是相同的。

以下我们使用** 类型变量 **--- 一种特殊的变量 --- 只用于表示类型而不是值。

```js
function identity<T>(arg: T): T {
    return arg;
}
```

我们给 identity 函数添加了类型变量T。T帮助我们捕获传入的类型，之后再使用T作为返回值的时候，我们就知道了返回值的类型。

允许我们跟踪函数里面使用的类型的信息。我们将这个版本的`identity`函数叫做**泛型**。

--

定义了泛型函数之后，使用方法有两种：

##### （1）传入所有的参数，包括类型参数：

```js
let output = identity<string>("myString");  // type of output will be 'string
```

##### （2）使用类型推论 ---  编译器会根据我们传入的参数自动的帮我们确定T的类型。【更普遍】

```js
let output = identity("myString");  // type of output will be 'string'
```

如果编译器不能够自动地推断出类型的话，只能像上面（1）那样明确的传入T的类型

--

## 2、泛型变量

使用泛型创建像`identity`这样的泛型函数时，编译器要求你在函数体必须**正确的使用**这个通用的类型。

也就是说你必须把这些参数当做是**所有的类型**。

例如：我们想打印出arg的长度。写法如下：

```js
function loggingIdentity<T>(arg: T): T {
    console.log(arg.length);      // Error: T可能为number类型，没有length属性
    return arg;
}
```

若我们操作的是`T`类型的数组，而不直接是`T`类型：

```js
 function loggingIdentity<T>(arg: T[]): T[] {
    console.log(arg.length);     // Array has a .length, so no more error
    return arg;
 }
```

> #### 对于 loggingIdentity 类型的理解：

泛型函数loggingIdentity，接收类型参数T 和 参数arg；参数arg是类型为T的数组，并且返回元素类型为T 的数组。

这样可以把泛型变量T 当做类型的一部分使用，而不是整个类型，增加灵活性。

还可以用这种写法：

```js
function loggingIdentity<T>(arg: Array<T>): Array<T> {
    console.log(arg.length);  // Array has a .length, so no more error
    return arg;
}
```

 ---

## 3、泛型接口

泛型函数与非泛型函数没什么不同，只是有一个类型参数在最前面，像函数声明一样。

```js
function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: <U>(arg: U) => U = identity;  // 可以使用不同的泛型参数名（对应即可）
```

 还可以使用带有调用签名的队形字面量来定义泛型函数。

```js
function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: {<T>(arg: T): T} = identity;
```



