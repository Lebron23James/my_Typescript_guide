> ## 简介

介绍几种TypeScript 中的高级类型

---

## 1、交叉类型（intersection types）

交叉类型是将现有的多个类型叠加到一起合并为一个类型 ，它包含了所需的所有类型的特性。

我们大多数是在混入（mixins）或 其他不适合典型面向对象模型的地方看到交叉类型的使用。

```js
function extend<T, U>(first: T, second: U): T & U {
    let result = <T & U>{};
    for (let id in first) {
        (<any>result)[id] = (<any>first)[id];
    }
    for (let id in second) {
        if (!result.hasOwnProperty(id)) {
            (<any>result)[id] = (<any>second)[id];
        }
    }
    return result;
}

class Person {
    constructor(public name: string) { }
}
interface Loggable {
    log(): void;
}
class ConsoleLogger implements Loggable {
    log() {
        // ...
    }
}
var jim = extend(new Person("Jim"), new ConsoleLogger());
var n = jim.name;
jim.log();
```

## 2、联合类型（Union Types）

联合类型与交叉类型很有关联，但是使用上却完全不同。

例如：一个代码库希望传入number 或string 类型的参数。如下所示：

```js
function padLeft(value: string, padding: any) {
    if (typeof padding === "number") {
        return Array(padding + 1).join(" ") + value;
    }
    if (typeof padding === "string") {
        return padding + value;
    }
    throw new Error(`Expected string or number, got '${padding}'.`);
}

padLeft("Hello world", 4); // returns "    Hello world"
```

以上，padding参数的类型为any，当我们传入一个既不是number，也不是string类型的参数：

```js
let indentedString = padLeft("Hello world", true); // 编译阶段通过，运行时报错
```

---

在传统的面向对象语言里，我们可能会将这两种类型抽象成有层级的类型。 这么做显然是非常清晰的，但同时也存在了过度设计。

padLeft原始版本的好处之一是允许我们传入原始类型。 这样做的话使用起来既简单又方便。 如果我们就是想使用已经存在的函数的话，这种新的方式就不适用了。

---

**联合类型** 表示一个值可以是几种类型之一，我们用竖线分割每种类型：

```js
 function padLeft(value: string, padding: string | number) {  // 这里表示一个值可以为string类型 或者 number类型
    // ...
}

let indentedString = padLeft("Hello world", true); // 编译阶段就回报错
```

---

如果一个值是联合类型，那么我们只能访问此联合类型的所有类型里共有的成员。

```js
interface Bird {
    fly();
    layEggs();
}

interface Fish {
    swim();
    layEggs();
}

function getSmallPet(): Fish | Bird {
    // ...
}

let pet = getSmallPet();
pet.layEggs(); // okay
pet.swim();    // errors
```

## 3、类型保护与区分类型（Type Guards and Differentiating Types）

JavaScript中常用来区分两个值的方法就是检查成员是否存在。

```js
let pet = getSmallPet();

// 每一个成员访问都会报错 --- 因为我们只能访问联合类型中共同拥有的成员。
if (pet.swim) {
    pet.swim();
}
else if (pet.fly) {
    pet.fly();
}
```

我们可以使用类型断言来是这段代码可以工作：

```js
let pet = getSmallPet();

if ((<Fish>pet).swim) {
    (<Fish>pet).swim();
}
else {
    (<Bird>pet).fly();
}
```

> #### 用户自定义的类型保护

想要实现：假若我们一旦检查过类型，就能在之后的每个分支里清楚滴知道pet 的类型。TypeScript里的`类型保护机制`可以实现。

**类型保护**就是一些表达式，它们会在运行时检查以确保在某个作用域里的类型。要定义一个类型保护，我们只要简单的定义一个函数，他的返回值是一个**类型谓词**：（`parameterName is Type 这种格式`）

```js
function isFish(pet: Fish | Bird): pet is Fish {
    return (<Fish>pet).swim !== undefined;
}
```

以上例子中，`pet  is Fish` 就是类型谓词；其中参数名\(pet\)必须来自当前函数签名里的一个参数名。

每当使用一些变量调用isFish 时，TypeScript会将变量缩减为那个具体的类型。只要这个类型与变量的原始类型是兼容的。

```js
// 'swim' 和 'fly' 调用都没有问题了

if (isFish(pet)) {
    pet.swim();
}
else {
    pet.fly();
}
```

TypeScript不仅知道在if 分支里pet 是Fish类型； 它还清楚再else 分支里一定不是Fish 类型，一定是Bird 类型。

> #### typeof 类型保护

现在我们回过头来看看怎么使用联合类型书写`padLeft`代码。 我们可以像下面这样利用类型断言来写：

```js
function isNumber(x: any): x is number {
    return typeof x === "number";
}

function isString(x: any): x is string {
    return typeof x === "string";
}

function padLeft(value: string, padding: string | number) {
    if (isNumber(padding)) {
        return Array(padding + 1).join(" ") + value;
    }
    if (isString(padding)) {
        return padding + value;
    }
    throw new Error(`Expected string or number, got '${padding}'.`);
}
```

但是，以上例子中必须定义一个函数来判断类型是否为原始类型，这非常的不方便；

在TypeScript中我们不必将 typeo x === 'number' 抽象成一个函数； 因为TypeScript可以将它识别为一个类型保护。

```js
function padLeft(value: string, padding: string | number) {
    if (typeof padding === "number") {
        return Array(padding + 1).join(" ") + value;
    }
    if (typeof padding === "string") {
        return padding + value;
    }
    throw new Error(`Expected string or number, got '${padding}'.`);
}
```



