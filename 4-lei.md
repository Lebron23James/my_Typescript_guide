> ## 简介

传统的JavaScript 是通过函数 和 基于原型的继承来创建可重用的组件。

ES6 能够使用基于类的面向对象的方式。

TypeScript 允许使用类的特性，并且编译后的js 可以在所有主流浏览器和平台上运行。

---

## 1、类的定义

```js
class Greeter {
    greeting: string;                        //属性greeting

    constructor (message: string) {          //构造函数
        this.greeting = message;
    }
    greet () {                               //方法greet
        return "Hello," + this.greeting;
    }
}

let greeter = new Greeter("world");          //创建了Greeter类的一个实例
```

引用类成员的时候会用this； this表示的我们访问的是类的成员。

new关键字调用之前定义的构造函数，并执行构造函数创建一个Greeter类型的新对象。

## 2、类的继承

基于类的程序设计中一种最基本的模式：允许使用继承扩展现有的类。

```js
class Animal {
    move(distanceInMeters: number = 0) {
        console.log(` Animal moved ${distanceInMeters}m `)
    }
}

class Dog extends Animal {
    bark() {
        console.log('woof!!');
    }   
}

const dog = new Dog();
dog.bark();
dog.move(10);
```

以上展示了最基本的类继承，类从基类中继承了属性和方法。

> Dog是一个派生类，派生自Animal基类，通过extends关键字； 派生类通常称为**子类**，基类通常称为**超类**。

以下例子创建了Animal的两个子类，描述了如何在子类中重写父类的方法：

```js
class Animal {
    name: string;
    constructor(theName: string) { this.name = theName; }
    move(distanceInMeters: number = 0) {
        console.log(`${this.name} moved ${distanceInMeters}m.`);
    }
}

class Snake extends Animal {
    constructor(name: string) { super(name); }
    move(distanceInMeters = 5) {
        console.log("Slithering...");
        super.move(distanceInMeters);
    }
}

class Horse extends Animal {
    constructor(name: string) { super(name); }
    move(distanceInMeters = 45) {
        console.log("Galloping...");
        super.move(distanceInMeters);
    }
}

let sam = new Snake("Sammy the Python"); 
let tom: Animal = new Horse("Tommy the Palomino");  //即使tom声明为Animal类型，但是它从Horse实例化而来，会调用Horse里的方法

sam.move();        // Slithering...
                   // Sammy the Python moved 5m

tom.move(34);      // Galloping...
                   // Tommy the Palomino moved 34m.
```

派生类中包含一个构造函数，必须调用super\(\) 来执行基类的构造函数。

在构造函数里访问this的属性之前，一定要调用super\(\)；**这是TypeScript强制执行的一条重要规则。**

Snake类 和 Horse类都创建了move方法，重写了从Animal继承来的move方法，使move方法根据不同的类有不同的功能。

## 3、类的修饰符

> #### （1）公共：public

在TypeScript中，成员默认为public类型的。

也可以明确的将一个成员标记 public， 重写上面的Animal 类如下：

```js
class Animal {
    public name: string;
    public constructor(theName: string) { this.name = theName; }
    public move(distanceInMeters: number = 0) {
        console.log(`${this.name} moved ${distanceInMeters}m.`);
    }
}
```

> #### （2）私有：private

成员被标记为private时，就不能在声明它的类外部访问。

```js
class Animal {
    private name: string;
    constructor(theName: string) { this.name = theName; }
}

new Animal("cat").name;  //报错， name是私有的
```

TypeScript 使用的是结构性的类型系统。

当我们比较两种不同的类型的时候，并不在乎它们从何处而来，如果所有的类型都是兼容的，我们就认为它们的类型是兼容的。

**但是**，比较带有private 或 protected 类型的成员时，情况就是不同 了：

> 如果其中一个类型包含private 或 protected 类型的成员，另外一个类型也存在这样一个private 或 protected 类型的成员，**并且它们都来自于同一处声明时**，我们才认为这两个类型是兼容的。

```js
class Animal {
    private name: string;
    constructor(theName: string) { this.name = theName; }
}

class Rhino extends Animal {
    constructor() { super("Rhino"); }
}

class Employee {
    private name: string;
    constructor(theName: string) { this.name = theName; }
}

let animal = new Animal("Goat");
let rhino = new Rhino();
let employee = new Employee("Bob");

animal = rhino;
animal = employee; // 错误: Animal 与 Employee 不兼容.
```

因为Animal  和 Rhino 共享 来自Animal 的私有成员，因此它们是兼容的。

然而Employee 类里的那个私有成员name ，不是Animal 类里面的那一个，类型不兼容。

> #### （3）受保护：protected

1、protected修饰符与private修饰符类似，不同的一点：**protected成员在派生类中仍然可以访问。**

```js
class Person {
    protected name: string;
    constructor(name: string) { this.name = name; }
}

class Employee extends Person {
    private department: string;

    constructor(name: string, department: string) {
        super(name)
        this.department = department;
    }

    public getElevatorPitch() {   // 可以通过Employee 类的实例方法访问
        return `Hello, my name is ${this.name} and I work in ${this.department}.`;
    }
}

let howard = new Employee("Howard", "Sales");
console.log(howard.getElevatorPitch());
console.log(howard.name); // 错误 ---  但是不能在Person 类外使用name
```

因为Employee类由Person 类派生而来，所以我们可以通过Employee 类的实例方法访问。

但是不能在Person 类外使用name。

2、构造函数可以被标记为protected； 这个类可以被继承，但是不能在包含它的类外被实例化。

```js
class Person {
    protected name: string;
    protected constructor(theName: string) { this.name = theName; }
}

// Employee 能够继承 Person
class Employee extends Person {
    private department: string;

    constructor(name: string, department: string) {
        super(name);
        this.department = department;
    }

    public getElevatorPitch() {
        return `Hello, my name is ${this.name} and I work in ${this.department}.`;
    }
}

let howard = new Employee("Howard", "Sales");
let john = new Person("John"); // 错误: 'Person' 的构造函数是被保护的.
```

> #### （4）只读：readonly

只读属性必须在声明时 或 构造函数实例化时 被初始化。

```js
class Octopus {
    readonly name: string;
    readonly numberOfLegs: number = 8;
    constructor (theName: string) {
        this.name = theName;
    }
}
let dad = new Octopus("Man with the 8 strong legs");
dad.name = "Man with the 3-piece suit"; // 错误! name 是只读的.
```

> #### 参数属性

```js
class Person {
    protected name: string;
    protected constructor(theName: string) { this.name = theName; }
}
```

如上，我们不得不定义一个受保护的成员`name`和一个构造函数参数`theName`在`Person`类里，并且立刻给`name`和`theName`赋值。

**参数属性 **可以方便地让我们在一个地方定义并初始化一个成员。 下面的例子是对之前`Animal`类的修改版，使用了参数属性：

```js
//原来版本
class Animal {
    name: string;
    constructor(theName: string) { this.name = theName; }
    move(distanceInMeters: number = 0) {
        console.log(`${this.name} moved ${distanceInMeters}m.`);
    }
}
```

**参数属性 **通过给构造函数参数添加一个访问限定符 来声明。将声明和赋值合并到一处。

* 使用private 限定一个参数属性会声明并初始化一个私有成员。

```js
//最新版本
class Animal {
    constructor(private name: string) { }  //此处 创建和初始化name成员
    move(distanceInMeters: number) {
        console.log(`${this.name} moved ${distanceInMeters}m.`);
    }
}
```

## 4、存取器

TypeScript支持通过 getters/setters 来截取对 对象成员的访问。 它能够帮助你有效控制对象成员的访问。

没有存取器的例子，可以随意设置fullName，但是会带来麻烦。

```js
class Employee {
    fullName: string;
}

let employee = new Employee();
employee.fullName = "Bob Smith";
if (employee.fullName) {
    console.log(employee.fullName);
}
```

将以上类设置为使用`get` 和 `set`方法，先检查用户密码是否正确，再修改员工信息。

```js
let passcode = "secret passcode";

class Employee {
    private _fullName: string;

    get fullName(): string {
        return this._fullName;
    }

    set fullName(newName: string) {
        if (passcode && passcode == "secret passcode") {
            this._fullName = newName;
        }
        else {
            console.log("Error: Unauthorized update of employee!");
        }
    }
}

let employee = new Employee();
employee.fullName = "Bob Smith";
if (employee.fullName) {
    alert(employee.fullName);
}
```

我们可以修改一下密码，来验证一下存取器是否是工作的。当密码不对时，会提示我们没有权限去修改员工。

对于存取器有以下几点需要注意：

（1）存取器要求你将编译器设置为 ECMAScript5 或更高（不支持降级到ECMAScript）；

（2）只带有get，不带有set 的存取器自动被推断为`readonly`；

这在从代码生成`.d.ts`文件时是有帮助的，因为利用这个属性的用户会看到不允许改变的值。

## 5、静态属性\(成员\)

**类的实例属性**：那些仅当类被实例化的时候才会被初始化的属性。———— 用`this.`来访问

**类的静态属性**：存在于类本身上面而不是类的实例实例上。【static声明】 ———— 用`类名.`来访问

```js
class Grid {
    static origin = {x: 0, y: 0};
    calculateDistanceFromOrigin(point: {x: number; y: number;}) {
        let xDist = (point.x - Grid.origin.x);
        let yDist = (point.y - Grid.origin.y);
        return Math.sqrt(xDist * xDist + yDist * yDist) / this.scale;
    }
    constructor (public scale: number) { }
}

let grid1 = new Grid(1.0);  // 1x scale
let grid2 = new Grid(5.0);  // 5x scale

console.log(grid1.calculateDistanceFromOrigin({x: 10, y: 10}));
console.log(grid2.calculateDistanceFromOrigin({x: 10, y: 10}));
```

以上例子中，使用static 来定义`origin`—— 所有网格都会用到的属性。

每个实例要想访问这个属性的时候，都要在 origin 前面加上类名（例如 Grid.），如同实例属性使用`this.`前缀访问属性一样。

## 6、抽象类

抽象类作为其他类的基类使用；他们一般不会直接被实例化。

不同于接口，抽象类可以包含成员的实现细节。

`abstract`关键字是用于定义抽象类 和 在抽象类内部定义抽象方法。

```js
abstract class Animal {
    abstrsct makeSound(): void;
    move(): void {
        console.log('I am string');
    }
}
```

抽象类的抽象方法不包含具体实现，并且必须在派生类中实现。

抽象方法的语法 与 接口方法相似。两者都是定义方法签名但不包含方法体。 然而，抽象方法必须包含`abstract`关键字并且可以包含访问修饰符。

```js
abstract class Department {

    constructor(public name: string) {
    }

    printName(): void {
        console.log('Department name: ' + this.name);
    }

    abstract printMeeting(): void; // 抽象方法 必须在派生类中实现
}

class AccountingDepartment extends Department {

    constructor() {
        super('Accounting and Auditing'); // 在派生类的构造函数中必须调用 super()
    }

    printMeeting(): void {
        console.log('The Accounting Department meets each Monday at 10am.');
    }

    generateReports(): void {
        console.log('Generating accounting reports...');  
    }
}

let department: Department;              // 允许创建一个对抽象类型的引用
department = new Department();           // 错误: 不能创建一个抽象类的实例
department = new AccountingDepartment(); // 允许对一个抽象子类进行实例化和赋值
department.printName();
department.printMeeting();
department.generateReports();             // 错误: 方法在声明的抽象类中不存在
```

## 7、高级技巧

（1）构造函数

当在TypeScript 中声明一个类的时候，同时声明了很多的东西。

> 1、声明了类的实例的类型。

```js
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello, " + this.greeting;
    }
}

let greeter: Greeter;            //Greeter类的实例的类型是Greeter
greeter = new Greeter("world");
console.log(greeter.greet());
```

2、创建了一个叫做构造函数丶值

（2）把类当做接口使用

