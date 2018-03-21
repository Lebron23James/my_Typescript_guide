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

protected修饰符与private修饰符类似，不同的一点：**protected成员在派生类中仍然可以访问。**













