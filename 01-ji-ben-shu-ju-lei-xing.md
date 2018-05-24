## 简介

TypeScript 除了支持JavaScript 中所有的数据类型之外，还提供了使用的枚举类型。

---

### 1、布尔值 **boolean**

在TypeScript 和 JavaScript中都叫做 **boolean** ，值为true 或 false；

```js
let isDone:boolean = false;
```

### 2、数字 number

在TypeScript 和 JavaScript中**所有的数字都是浮点数**，这些浮点数的类型是** number**。

除了支持十进制和十六进制字面量，TypeScript中还支持 ES6中引入的二进制和八进制字面量。

```js
let decLiteral: number = 6;                //十进制
let hexLiteral: number = 0xf00d;           //十六进制 
let binaryLiteral: number = 0b1010;        //二进制
let octalLiteral: number = 0o774;          //八进制
```

### 3、字符串 **string**

在TypeScript 和 JavaScript中所有文本数据类型用**string **表示；使用单引号（‘’）或者双引号（“”）来表示。

```js
let name:string = "Bob";
name = "smith";
```

还可以使用**模板字符串**【定义多行文本和内嵌表达式】

```js
let name: string = "janny";
let age: number = 23;

let sentence: string = ` Hello, my name is ${name}，
                         I am ${age} years old;  `
```

### 4、数组

TypeScript 和 JavaScript一样可以操作数组元素。 有两种方式可以定义数组：

> 第一种： 在元素类型后面接上\[ \]； 表示有此类型元素组成的一个数组

```js
let list:number[] = [1, 2, 3];
```

> 第二种： 使用数组泛型， **Array&lt;元素类型&gt;**

```js
let list: Array<number> = [1, 2, 3];
```

### 5、元组 **Tuple**

元组类型表示一个**已知元素数量和类型数组**，其中各元素的类型不必相同。

```js
let x: [string, number];   //定义一对值分别为string和number类型的元组
x = ["hello", 10];         //true
x = [10, "hello"];         //false
```

当访问一个已知索引的类型，会得到正确的值。

```js
console.log(x[0].substr(1));  // OK
console.log(x[1].substr(1));  // Error  number类型没有substr 方法
```

当访问一个越界元素的时候，会使用联合类型代替。

```js
x[3] = "world";  //OK 字符串可以赋值给（string | number）类型
x[5].toString();  //OK String和number都有toString方法
x[6] = true;      //Error, 布尔不是（string | number）类型
```

### 6、枚举**enum**

枚举类型是对JavaScript标准数据类型的一个补充，可以**为一组数值赋予友好的名字**。

```js
enum Color {red, green, blue}
let c:Color = Color.green;        //1
```

默认的从0开始编号，也可以手动为成员指定数值。

```js
enum Color {red=1, green, blue}
let c:Color = Color.green;        //2
```

也可以全部采用手动赋值。

```js
enum Color {red=1, green=3, blue=4}
let c:Color = Color.green;        //3
```

枚举类型提供了一个便利：他可以**通过枚举成员的数值来得到它的名字**。

```js
enum Color {red=1, green=2, blue=4}
let colorName:string = Color[2];   // green
```

### 7、any

有时候，我们想要为那些在编译阶段还不清楚类型的变量指定一个类型。 这种情况下我们不希望类型检查器对这些值进行类型检查，而是直接让他们通过编译阶段的检查。此时可以使用any类型来标记这些变量。

```js
let notSure:any = 4;
notSure = "I am string";     //OK
notSure = false;             //OK
```

对现有类型进行改写的时候，any类型是十分有用的，它允许你在编译的时候可以选择性的包含或移除类型检查！！！

> 但是Object类型的变量只允许你给它赋予任意值，不能够在它上面调用任意的方法（即使它真的有这些方法）

```js
let notSure:any = 4;
notSure.ifItExist();  //OK
notSure.toFixed();    //OK

let prettySure: Object = 4;
prerrySure.toFixed();  //Error
```

当只知道一部分数据类型的时候，any类型也是有用的。

```js
let list:any[] = [4, true, "srring"];
list[1] = 100;  //OK
```

### 8、void

void类型与any类型相反，表示没有任何类型。当一个函数没有返回值的时候，你会看到他的返回值类型是void。

```js
function warnUser(): void {
   alert("This is a warning message");
}
```

声明一个void类型的变量没有意义，因为你只能为它赋值undefined 或 null。

```js
let unusable: void = undefined;
```

### 9、null 和 undefined

TypeScript 中unll 和 undefined各自有自己的类型，分别叫做 null 和 undefined。和void类似，他们本身类型用处不是很大。

```js
let u:undefined = undefined;
let n:null = null;
```

默认情况下，null和undefined 是所有类型的子类型。（可以把null和undefined赋值给number类型的变量）

**注意：** 当你指定`--strictNullChecks `标记，null和undefined只能赋值给void和他们各自。这能避免很多问题。

### 10、never

never类型表示的是那些永不存在的值的类型。例如：never类型是那些总是会抛出异常  或者  根本不会有返回值的函数表达式、箭头函数表达式的返回值类型； 变量也可能是never类型，当它们被永不为真的类型保护所约束时。

下面是一些返回never类型的函数：

```js
// 返回never类型的函数必须存在无法到达的终点
function error(message: string) :never {
   throw new Error(message);
}

// 推断的返回值类型为never
function fail(): never {
   return error("someting failed");
}

// 返回never类型的函数必须存在无法到达的终点
function infinitLoop(): never {
   while(true) {}
}
```

### 11、类型断言

当你比TypeScript更了解某个值的详细信息，你清楚的知道这个值比它现有类型更确切的类型，使用类型断言。

**类型断言**好比其他语言里的类型转换，但是不进行特殊的数据检查和结解构。他没有运行时的影响，只是在编译阶段起作用。TypeScript会假设你已经进行了必要的检查。

类型断言有两种形式，

> 第一种：尖括号语法

```js
let someValue: any = "this is a string";
let stringLength: number = (<string>someValue).length;
```

> 第二种：as语法

```js
let someValue: any = "this is a string";
let stringLength: number = (someValue as string).length;
```

这两种形式是等价的，但是当你在TypeScript里使用JSX时，只有as语法的断言是被允许的。

