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

以上TypeScript类型保护只有两种形式被识别：`typeof v === "typename"`和`typeof v !== "typename"`

“typename” 必须是 `number`  `string`  `boolean`  `symbol。`

但是TypeScript并不会阻止你与其它字符串比较，语言不会把那些表达式识别为类型保护。

> #### instanceof 类型保护

instanceof 类型保护是通过构造函数来细化类型 一种方式。

```js
interface Padder {
    getPaddingString(): string
}

class SpaceRepeatingPadder implements Padder {
    constructor(private numSpaces: number) { }
    getPaddingString() {
        return Array(this.numSpaces + 1).join(" ");
    }
}

class StringPadder implements Padder {
    constructor(private value: string) { }
    getPaddingString() {
        return this.value;
    }
}

function getRandomPadder() {
    return Math.random() < 0.5 ?
        new SpaceRepeatingPadder(4) :
        new StringPadder("  ");
}

// 类型为SpaceRepeatingPadder | StringPadder
let padder: Padder = getRandomPadder();

if (padder instanceof SpaceRepeatingPadder) {
    padder; // 类型细化为'SpaceRepeatingPadder'
}
if (padder instanceof StringPadder) {
    padder; // 类型细化为'StringPadder'
}
```

instanceof 的右侧要求是一个构造函数，TypeScript 将细化为：

1. 此构造函数的prototype 属性的类型（如果类型不为any）
2. 构造签名所返回的类型的联合

## 4、可以为null 的类型

TypeScript具有两种特殊的类型：null 和 undefined， 它们的值分别为null 和 undefined。

默认情况下，类型检查器认为null 和 undefined 可以赋值任意类型； null和undefined是所有其他类型的一个有效值；

也就是说你阻止不了将它们赋值给其他类型。

`--strictNullChecks`  标记可以解决此问题，也就是说当你声明一个变量的时候，它不会自动的包含null和undefined。

你可以使用联合类型明确的包含null和undefined：

```js
let s = "foo";
s = null; // 错误, 'null'不能赋值给'string'
let sn: string | null = "bar";
sn = null; // 可以

sn = undefined; // error, 'undefined'不能赋值给'string | null'
```

> #### 关于可选参数 和 可选属性

使用了`--strictNullChecks`，可选参数会被自动的加上`|undefined`

```js
function f(x: number, y?: number) {
    return x + (y || 0);
}
f(1, 2);
f(1);
f(1, undefined);
f(1, null); // error, 'null' is not assignable to 'number | undefined'
```

可选属性也会做同样的处理：

```js
class C {
    a: number;
    b?: number;
}
let c = new C();
c.a = 12;
c.a = undefined; // error, 'undefined' is not assignable to 'number'
c.b = 13;
c.b = undefined; // ok
c.b = null; // error, 'null' is not assignable to 'number | undefined'
```

> #### 类型保护和类型断言

由于可以为null 的类型是通过联合类型实现的，你需要使用类型保护来去除 null

```js
function f(sn: string | null): string {
    if (sn == null) {
        return "default";
    }
    else {
        return sn;
    }
}
```

还可以使用短路运算符来去除null：

```js
function f(sn: string | null): string {
    return sn || "default";
}
```

如果编译器不能去除null 和 undefined，你可以使用类型断言手动去除null 和 undefined；

语法为添加`！` 后缀，identifier！从identifier 的类型里去除了null 和 undefined。

```js
function broken(name: string | null): string {
  function postfix(epithet: string) {
    return name.charAt(0) + '.  the ' + epithet; // error, 'name' is possibly null
  }
  name = name || "Bob";
  return postfix("great");
}

function fixed(name: string | null): string {
  function postfix(epithet: string) {
    return name!.charAt(0) + '.  the ' + epithet; // ok
  }
  name = name || "Bob";
  return postfix("great");
}
```

本例使用了嵌套函数，因为编译器无法去除嵌套函数的null（除非是立即调用的函数表达式）。

因为它无法跟踪所有对嵌套函数的调用，尤其是你将内层函数做为外层函数的返回值。

如果无法知道函数在哪里被调用，就无法知道调用时`name`的类型。

## 5、类型别名

**类型别名**就是会给一个类型起个新的名字----来引用这个类型；类型别名不会新建一个类型。

给原始类型起别名通常没有什么用，尽管可以作为文档的一种形式使用。

```js
type Name = string;
type NameResolver = () => string;
type NameOrResolver = Name | NameResolver;
function getName(n: NameOrResolver): Name {
    if (typeof n === 'string') {
        return n;
    }
    else {
        return n();
    }
}
```

**类型别名**有时和`接口`很像--- 可以作用于原始值、联合类型、元组等。

**类型别名**可以是`泛型`： 我们可以添加类型参数，并且在别名声明的右侧传入。

```js
type Container<T> = { value: T };
```

**类型别名**也可以在属性里自己引用自己：

```js
type Tree<T> = {
    value: T;
    left: Tree<T>;
    right: Tree<T>;
}
```

类型别名与交叉类型一起使用，我们可以创造出十分古怪的类型：

```js
type LinkedList<T> = T & { next: LinkedList<T> };

interface Person {
    name: string;
}

var people: LinkedList<Person>;
var s = people.name;
var s = people.next.name;
var s = people.next.next.name;
var s = people.next.next.next.name;
```

然而，类型别名不能出现在声明右侧的任何地方。

```js
type Yikes = Array<Yikes>; // error
```



