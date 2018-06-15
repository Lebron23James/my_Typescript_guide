> ## 简介

**泛型** 可以用来创建可重用的组件，一个组件可以支持多种类型的数据。这样用户就可以以自己的数据类型来使用组件。

可以创建 泛型接口 和 泛型类；但是无法创建 泛型枚举 和 泛型命名空间。

---

## 1、泛型 -- hello  world

使用`any` 类型来定义函数可以接受任何类型的`arg`参数。但是这样会丢失一些信息：传入的类型与返回的类型应该是相同的。

```js
function identity(arg: any): any {
    return arg;
}
```

因此需要一种方法使返回值的类型与传入参数的类型是相同的。

以下我们使用**  类型变量 **--- 一种特殊的变量 --- 只用于表示类型而不是值。

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

## 3、泛型接口

泛型函数与非泛型函数没什么不同，只是有一个**类型参数**在最前面，像函数声明一样。

```js
function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: <U>(arg: U) => U = identity;  // 可以使用不同的泛型参数名（对应即可）
```

还可以使用带有调用签名的对象字面量来定义泛型函数。

```js
function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: {<T>(arg: T): T} = identity;
```

接下来将上面的对象字面量为一个接口，来定义泛型接口。

```js
interface GenericIdentityFn {
    <T>(arg: T): T;
}

function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: GenericIdentityFn = identity;
```

我们可以把泛型参数当做整个接口的一个参数，这样就能清楚的知道使用的是哪个泛型类型了 。

```js
interface GenericIdentityFn<T> {
    (arg: T): T;
}

function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: GenericIdentityFn<number> = identity;
```

```markdown
以上不再描述泛型函数，而是把非泛型函数签名作为泛型类型一部分。 
当我们使用 GenericIdentityFn的时候，还得传入一个类型参数来指定泛型类型（这里是：number），锁定了之后代码里使用的类型。 
对于描述哪部分类型属于泛型部分来说，理解何时把参数放在调用签名里和何时放在接口上是很有帮助的。
```

## 4、泛型类

泛型类将泛型类型放在（&lt;&gt;）中，跟在类名后面。

```js
class GenericNumber<T> {
    zeroValue: T;
    add: (x: T, y: T) => T;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function(x, y) { return x + y; };

let stringNumeric = new GenericNumber<string>();
stringNumeric.zeroValue = "";
stringNumeric.add = function(x, y) { return x + y; };

alert(stringNumeric.add(stringNumeric.zeroValue, "test"));
```

与接口一样，直接把泛型类型写在类的后面，可以确认类的所有属性都在使用相同的类型。

**注意**：泛型类指的是实例部分的类型，所以类的静态属性不能使用这个泛型类型。

## 5、泛型约束

情形：有时我们想要操作某类型的一组值，并且我们知道这组值具有什么样的属性。如下：

```js
function loggingIdentity<T>(arg: T): T {
    console.log(arg.length);  // Error: 这里编译器不能证明每种类型都有length属性
    return arg;
}
```

为此我们定义一个接口来描述约束条件： 创建一个包含 .length 属性的接口，使用这个**接口和extends关键字**来实现约束。

```js
interface Lengthwise {
    length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
    console.log(arg.length);  // Now we know it has a .length property, so no more error
    return arg;
}
```

以上的泛型函数定义了约束，因此它不在适用于任何类型：

```js
loggingIdentity(3);        //报错， number没有length属性
```

我们需要传入符合约束类型的值，必须包含必须的属性：

```js
loggingIdentity({length: 10, value: 3});
```

##### （1）在泛型约束中使用类型参数

```js
function getProperty(obj: T, key: K) {
    return obj[key];
}

let x = { a: 1, b: 2, c: 3, d: 4 };

getProperty(x, "a"); // okay
getProperty(x, "m"); // error: Argument of type 'm' isn't assignable to 'a' | 'b' | 'c' | 'd'.
```

##### （2）在泛型里使用类类型？？？

在TypeScript使用泛型创建工厂函数时，需要引用构造函数的类类型

```js
function create<T>(c: {new(): T; }): T {
    return new c();
}
```

一个更高级的例子，使用原型属性推断并约束构造函数与类实例的关系。

```js
class BeeKeeper {
    hasMask: boolean;
}

class ZooKeeper {
    nametag: string;
}

class Animal {
    numLegs: number;
}

class Bee extends Animal {
    keeper: BeeKeeper;
}

class Lion extends Animal {
    keeper: ZooKeeper;
}

function createInstance<A extends Animal>(c: new () => A): A {
    return new c();
}

createInstance(Lion).keeper.nametag;  // typechecks!
createInstance(Bee).keeper.hasMask;   // typechecks!
```



