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

联合类型











