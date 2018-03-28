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

```js
function buildName(firstName: string, ...restOfName: string[]) {
  return firstName + " " + restOfName.join(" ");
}

let buildNameFun: (fname: string, ...rest: string[]) => string = buildName;
```

## 5、this 与 箭头函数

JavaScript中，this的值在函数被调用的时候才会指定。

以下例子的中，`createCardPicker` 是一个函数，返回了另外一个函数。

运行程序的时候就会报错 ———因为调用`cardPicker()`的时候，this指向的是`window`。【严格模式下this为undefined】

```js
let deck = {
    suits: ["hearts", "spades", "clubs", "diamonds"],
    cards: Array(52),
    createCardPicker: function() {
        return function() {
            let pickedCard = Math.floor(Math.random() * 52);
            let pickedSuit = Math.floor(pickedCard / 13);

            return {suit: this.suits[pickedSuit], card: pickedCard % 13};
        }
    }
}

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();

alert("card: " + pickedCard.card + " of " + pickedCard.suit);
```

解决这个问题，就要求函数返回的时候就绑定好了正确的this。**箭头函数**能够保存创建时候的this，而不是调用时候的this。

```js
let deck = {
    suits: ["hearts", "spades", "clubs", "diamonds"],
    cards: Array(52),
    createCardPicker: function() {
        // NOTE: the line below is now an arrow function, allowing us to capture 'this' right here（箭头函数）
        return () => {
            let pickedCard = Math.floor(Math.random() * 52);
            let pickedSuit = Math.floor(pickedCard / 13);

            return {suit: this.suits[pickedSuit], card: pickedCard % 13};
        }
    }
}

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();

alert("card: " + pickedCard.card + " of " + pickedCard.suit);
```

#### this参数

以上例子中 `this.suits[pickedSuit]`里的`this`的类型为`any` ；若给编译器设置`--noImplicitThis`标记，TypeScript就会警告。

因为this参数是一个假的参数，改变的方法就是提供一个显式的this参数。

```js
//添加一些接口，让类型重用变得简单清晰
interface Card {
    suit: string;
    card: number;
}
interface Deck {
    suits: string[];
    cards: number[];
    createCardPicker(this: Deck): () => Card;
}
let deck: Deck = {
    suits: ["hearts", "spades", "clubs", "diamonds"],
    cards: Array(52),
    // NOTE: The function now explicitly specifies that its callee must be of type Deck

    createCardPicker: function(this: Deck) {    //this变为Deck类型的
        return () => {
            let pickedCard = Math.floor(Math.random() * 52);
            let pickedSuit = Math.floor(pickedCard / 13);

            return {suit: this.suits[pickedSuit], card: pickedCard % 13};
        }
    }
}

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();

alert("card: " + pickedCard.card + " of " + pickedCard.suit);
```

this参数在回调函数里？？

## 6、重载

JavaScript 是动态语言，根据传入的不同参数而返回不同类型的数据。

如果传入的是代表纸牌的对象，函数作用是从中抓一张牌。 如果用户想抓牌，我们告诉他抓到了什么牌。

```js
let suits = ["hearts", "spades", "clubs", "diamonds"];

function pickCard(x): any {
    // Check to see if we're working with an object/array
    // if so, they gave us the deck and we'll pick the card
    if (typeof x == "object") {
        let pickedCard = Math.floor(Math.random() * x.length);
        return pickedCard;
    }
    // Otherwise just let them pick the card
    else if (typeof x == "number") {
        let pickedSuit = Math.floor(x / 13);
        return { suit: suits[pickedSuit], card: x % 13 };
    }
}

let myDeck = [{ suit: "diamonds", card: 2 }, { suit: "spades", card: 10 }, { suit: "hearts", card: 4 }];
let pickedCard1 = myDeck[pickCard(myDeck)];
alert("card: " + pickedCard1.card + " of " + pickedCard1.suit);

let pickedCard2 = pickCard(15);
alert("card: " + pickedCard2.card + " of " + pickedCard2.suit);
```

以上函数在类型系统中表示的方法：**为同一个函数提供多个函数类型定义来进行函数重载**。

编译器会根据这个列表去处理函数的调用。 重载的`pickCard`函数在调用的时候会进行正确的类型检查。

```js
let suits = ["hearts", "spades", "clubs", "diamonds"];

function pickCard(x: {suit: string; card: number; }[]): number;    //重载1：接受对象
function pickCard(x: number): {suit: string; card: number; };      //重载2：接受数字
function pickCard(x): any {
    // Check to see if we're working with an object/array
    // if so, they gave us the deck and we'll pick the card
    if (typeof x == "object") {
        let pickedCard = Math.floor(Math.random() * x.length);
        return pickedCard;
    }
    // Otherwise just let them pick the card
    else if (typeof x == "number") {
        let pickedSuit = Math.floor(x / 13);
        return { suit: suits[pickedSuit], card: x % 13 };
    }
}

let myDeck = [{ suit: "diamonds", card: 2 }, { suit: "spades", card: 10 }, { suit: "hearts", card: 4 }];
let pickedCard1 = myDeck[pickCard(myDeck)];
alert("card: " + pickedCard1.card + " of " + pickedCard1.suit);

let pickedCard2 = pickCard(15);
alert("card: " + pickedCard2.card + " of " + pickedCard2.suit);
```

为了让编译器能够选择正确的检查类型，它与JavaScript里的处理流程相似。 它查找重载列表，尝试使用第一个重载定义。

 如果匹配的话就使用这个。 因此，在定义重载的时候，一定要把最精确的定义放在最前面。

