> ## 简介

TypeScript 的核心原则之一就是对值所具有的结构进行类型检查。 又是被称作“鸭式辨型法” 或 “结构性子类型化”。

接口的作用就是：为这些类型命名、为你的代码或者第三方代码定义契约。

---

## 1、接口初探

```js
function printLabel ( labelObj: {label:string} ) {
    console.log(labelObj.label);
}

let myObj = { size:10, label:"i am string" };
printLabel( myObj );
```

类型检查器会查看printLabel的调用：对象参数中有一个名为label、类型为string的属性。

传入的对象有很多属性，但是编译器只会检查那些必须的属性是否存在、类型是否匹配。

> 上述的例子使用接口来描述：

```js
interface labelLedValue {label: string;}
function printLabel ( labelObj: labelLedValue  ) {
    console.log(labelObj.label);
}

let myObj = { size:10, label:"i am string" };
printLabel( myObj );
```

labelLedValue接口好比是一个名字，用来描述上面例子里的要求。

> **注意**：类型检查器不会检查属性的顺序，只要相应的属性存在并且类型正确就可以。

## 2、可选属性

接口里的属性不全都是必须的。可选属性的定义只是在属性名字后面加一个 “？”符号。

可选属性在应用“option bags”模式时很常用，即给函数传入的参数对象只有部分属性赋值了。例子：

```js
interface SquareConfig {
    color? : string;
    width? : number;
}
function createSquare (config: SquareConfig): {color: string; area:number} {
    let newSquare = {color: "red", area: 100};
    if (config.color) {
        newSquare.color = config.color;
    }
    if (config.area) {
        newSquare.area = config.width * config.width;
    }
    return newSquare;
}
let mySquare = createSquare({color: "black"});
```

可选属性的好处：

> （1）对可能存在的属性进行预定义；
>
> （2）可以捕获引用了不存在的属性时的错误。

例如： 我们故意将createSquaer 里面的属性名拼错，就回得到一个错误提示。

```js
interface SquareConfig {
    color? : string;
    width? : number;
}
function createSquare (config: SquareConfig): {color: string; area:number} {
    let newSquare = {color: "red", area: 100};
    if (config.col) {
        // Error: Property 'col' does not exist on type 'SquareConfig'
        newSquare.color = config.color;
    }
    if (config.area) {
        newSquare.area = config.width * config.width;
    }
    return newSquare;
}
let mySquare = createSquare({color: "black"});
```

## 3、只读属性

一些对象属性只能在对象刚刚创建时能够修改其值；可以在属性名之前加readonly类指定为只读属性。

```js
interface Point {
    readonly x: number;
    readonly y: number;
}
let p：point = { x:10, y:20 };
p.x = 23;  // error
```

TypeScript具有** ReadonlyArray&lt;T&gt; **类型，他与 **Array&lt;T&gt; **相似，只是把所有的可变的方法都去掉了，确保数组创建之后不能被修改。

```js
let a: number[] = [1, 2, 3, 4];
let ro: ReadonlyArray<number> = a;
ro[0] = 12;          // error
ro.push(5);          // error
ro.length = 100;     //error
a = ro;              // error   即使把ReadonlyArray<T> 类型的数组赋值给普通数组也是不可以的！！
```

可以使用**类型断言**重写最后一行。为其赋值。

```js
a = ro as number[];
```

> #### readonly VS const

作为变量使用的话用 const， 作为属性使用的话用 readonly。

## 4、额外的属性检查

```js
interface SquareConfig {
    color?: string;
    width?: number;
}

function createSquare(config: SquareConfig): { color: string; area: number } {
    // ...
}

let mySquare = createSquare({ colour: "red", width: 100 }); // error: 'colour' not expected in type 'SquareConfig'
```

TypeScript会认为此段代码存在bug。对象字面量【赋值给变量 或者 作为参数传递 时候】会被特殊对待且经过**额外的属性检查。**

**绕开这些额外的属性检查的方法：**

> #### （1）使用类型断言。

```js
let mySquare = createSquare({ width:100, height:50 } as SquareConfig );
```

> #### （2）添加一个字符串签名索引。【最佳】

前提是你能够确定这个对象可能具有某些作为特殊用途使用的额外属性。

```js
interface SquareConfig {
    color?: string;
    width?: number;
    [propName: string]: any;
}
```

> #### （3）将对象赋值给一个变量。

因为变量不会经过额外的属性检查，所以编译不会报错。

```js
let squaerOptions = { width:100, height:50 };
let mySquare = createSquare(squaerOptions);
```

## 5、接口描述 -- 函数类型

为了使用接口表示函数类型，我们需要给接口定义一个**调用签名**。

> 它就像一个只有参数列表和返回值类型 的函数定义； 参数列表里的每个参数都需要名字和类型。

```js
interface searchFunc {
    (source: string, substr: string): boolean;
}
```

这样定义之后我们就可以像使用其他接口一样使用这个函数类型的接口。

```js
let mySearch : searchFunc ;
mySearch = function(source: string, substr: string):boolean {
    let result = source.search(substr);
    return result > -1 ;
}
```

对于函数的类型检查来说，函数的参数名不需要与接口里定义的名字相匹配。

```js
let mySearch = searchFunc ;
mySearch = function(src: string, sub: string):boolean {
    let result = source.search(substr);
    return result > -1 ;
}
```

函数的参数会逐个进行检查，要求对应位置上的参数类型是兼容的。若不指定类型，TypeScript的类型系统会推断出参数类型。

函数的返回值类型也是通过返回值推断出来的（此处为true或false）；若函数返回数字或字符串，类型检查器会发出警告。

```js
let mySearch: SearchFunc;
mySearch = function(src, sub) {
    let result = src.search(sub);
    return result > -1;
}
```

## 6、接口描述 -- 可索引类型

可索引类型：具有一个**索引签名**，它描述了对象索引的类型，还有相应索引返回的类型。

```js
interface stringArray {
    [index: number] : string;      //number 类型的索引签名
}
let myArray: stringArray;
myArray = ["sos", "bob"];

let myStr: string = myArray[0];   //表示用number去索引具有stringArray接口的可遍历结构时，会得到string类型的返回值
```

> #### 索引签名有两种类型： 数字 和 字符串

可以同时使用两种类型的索引，但是数字索引的返回值必须是字符串索引返回值类型的子类型。

因为当使用number 来索引时，js会将它转变成string，然后再去索引对象。

```
   也就是100去索引等同于“100”去索引，因此两者需保持一致。
```

```js
class Animal {
    name: string;
}
class Dog extends Animal {
    breed: string;
}

// 错误：使用数值型的字符串索引，有时会得到完全不同的Animal!!!!!!!
interface NotOkay {
    [x: number]: Animal;
    [x: string]: Dog;
}
```

字符串索引声明了obj.property 和 obj\["property"\] 两种形式都可以。

字符串索引签名能够很好的描述`dictionary`模式，并且它们也会确保所有属性与其返回值类型相匹配。

```js
interface NumberDictionary {
     [index: string]: number;
     length: number;

     name: string;   //error  name的类型与索引类型返回值的类型不匹配
}
```

你还可以将索引签名设置为只读，这样就防止给索引赋值。

```js
interface ReadonlyStringArray {
    readonly [index: number]: string;
}
let myArray:ReadonlyStringArray = ["aaa", "bbb"];
myArray[2] = "ccc";   //error
```

## 7、接口描述 -- 类类型   【？？？？】

TypeScript 也可以用接口来明确的强制一个类去符合某种契约。

也可以在接口中描述一个方法，在类里面实现它\(如：setTime方法\)。【 **implements 是类实现一个接口的关键字。】**

```js
interface ClockInterface {
    currentTime: Date;
    setTime(d: Date);
}

class Clock implements ClockInterface {
    currentTime: Date;
    setTime(d: Date) {
        this.currentTime = d;
    }
    constructor(h: number, m: number) { }
}
```

接口描述了类的公共部分，不是公共和私有两部分；它不会帮你检查类是否有某些私有成员。

> #### 类的静态部分 与 实例部分的区别

类是具有两个类型的： 静态部分的类型  和  实例的类型。

当你定义一个接口，并试图定义一个类去实现 接口时会得到一个**错误**：

```js
interface ClockInterface {
    new (hour: number, minute: number);
}

class Clock implements ClockInterface {
    currentTime: Date;                            //实例部分
    constructor(h: number, m: number) { }         //静态部分
}
```

因为当一个类实现一个接口的时候，只对实例部分进行类型检查。constructor存在于类的静态部分，不在检查范围内。

因此，我们应该直接操作类的静态部分。如下：？？？

## 

## 8、接口继承

接口和类一样也可以实现相互继承。这让我们能从一个成员里复制成员到另一个接口，可以更灵活的将接口分割到可重用的模快里。

```js
interface Shape {
    color: string;
}
interface Square extends Shape {
    sideLength: number;
}

let square = <Square>{};
square.color = "blue";
square.sideLength = 10;
```

一个接口可以继承多个接口，创建出多个接口的合成接口。

```js
interface Shape {
    color: string;
}
interface PenStroke {
    penWidth: number;
}
interface Square extends Shape, PenStroke {
    sideLength: number;
}

let square = <Square>{};
square.color = "blue";
square.penWidth = 10;
square.sideLength = 5.0;
```

## 9、混合类型

接口能够描述JavaScript里丰富的类型。有时你希望一个对象能够具有上面提到的多种类型。

以下是一个例子：一个对象可以同时作为函数和对象使用，并带有额外的属性。

```js
interface Counter {
    (start: number): string;
    interval: number;
    reset(): void;
}
function getCounter(): Counter {
    let countner = <Counter>function (start:number) { };    //这是啥
    counter.interval = 123;
    counter.reset = function () { };
    return counter;
}

let c = getCounter();
c(10);                    // 作为函数
c.reset();                // 作为对象
c.interval = 5.0;         // 作为对象
```

使用第三方库的时候，你可能需要像上面那样完整的定义类型。

## 10、接口继承类

当接口继承一个类类型时，他会继承类的成员，但是不包括其实现。

即 接口声明了类中所有存在的成员，但是并没有提供具体实现。

接口会继承类的private 和 protected 成员；但**这个接口类型只能被这个类或其子类所实现**\(implement\)。

```js
class Control {
    private state: any;
}

interface SelectableControl extends Control {  //因为state是私有成员，所以只能是Control的子类才能够实现SelectableControl接口
    select(): void;                            //因为只有Control的子类才能够拥有一个声明于control的私有state           
}                                              //这对私有成员兼容性是必须的!        

class Button extends Control implements SelectableControl {
    select() { };
}

class TextBox exteneds Control {            
    select() { }
}

//、、、、、、、、、、

class Image implements SelectableControl {  //错误 ：Image；类型“state”属性
    select() { }
}

class Location {
}
```

在Control类内部允许通过SelectableControl 的实例成员来访问私有成员state的。

实际上SelectableControl就像control一样，并拥有一个select方法。

Button和TextBox类 是SelectableControl的子类（因为它们都继承自control并有select方法）。



