> ## 简介

TypeScript 的核心原则之一就是对值所具有的结构进行类型检查。 又是被称作“鸭式辨型法” 或 “结构性子类型化”。

接口的作用就是：为这些类型命名、为你的代码或者第三方代码定义契约。

---

## 1、接口初探

```
function printLabel ( labelObj: {label:string} ) {
    console.log(labelObj.label);
}

let myObj = { size:10, label:"i am string" };
printLabel( myObj );
```

类型检查器会查看printLabel的调用：对象参数中有一个名为label、类型为string的属性。

传入的对象有很多属性，但是编译器只会检查那些必须的属性是否存在、类型是否匹配。

> 上述的例子使用接口来描述：

```
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

```
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

> （2）可以捕获引用了不存在的属性时的错误。

例如： 我们故意将createSquaer 里面的属性名拼错，就回得到一个错误提示。

```
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

```
interface Point {
    readonly x: number;
    readonly y: number;
}
let p：point = { x:10, y:20 };
p.x = 23;  // error
```

TypeScript具有** ReadonlyArray&lt;T&gt; **类型，他与 **Array&lt;T&gt; **相似，只是把所有的可变的方法都去掉了，确保数组创建之后不能被修改。

```
let a: number[] = [1, 2, 3, 4];
let ro: ReadonlyArray<number> = a;
ro[0] = 12;          // error
ro.push(5);          // error
ro.length = 100;     //error
a = ro;              // error   即使把ReadonlyArray<T> 类型的数组赋值给普通数组也是不可以的！！
```

可以使用**类型断言**重写最后一行。

```
a = ro as number[];
```

> #### readonly VS const

作为变量使用的话用 const， 作为属性使用的话用 readonly。

## 4、额外的属性检查

```
interface SquareConfig {
    color?: string;
    width?: number;
}

function createSquare(config: SquareConfig): { color: string; area: number } {
    // ...
}

let mySquare = createSquare({ colour: "red", width: 100 }); // error: 'colour' not expected in type 'SquareConfig'
```

TypeScript会认为此段代码存在bug。对象字面量【赋值给变量或者作为参数传递 时候】会被特殊对待且经过**额外的属性检查。**

**绕开这些额外的属性检查的方法：**

> #### （1）使用类型断言。

```
let mySquare = createSquare({ width:100, height:50 } as SquareConfig );
```

> #### （2）添加一个字符串签名索引。【最佳】

前提是你能够确定这个对象可能具有某些作为特殊用途使用的额外属性。

```
interface SquareConfig {
    color?: string;
    width?: number;
    [propName: string]: any;
}
```

> #### （3）将对象赋值给一个变量。

以为变量不会经过额外的属性检查，所以编译不会报错。

```
let squaerOptions = { width:100, height:50 };
let mySquare = createSquare(squaerOptions);
```













