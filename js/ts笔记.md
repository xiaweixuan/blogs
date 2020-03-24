#### 基础类型

基本类型：number、string、boolean、symbol、null、undefined

还有Object，

除了上了的类型，ts还添加了下面一些类型：数组、元组、枚举、Ang、void、Never

```typescript
//定义数组
let list:number[]=[1,2,3]
let list:Array<number>=[1,2,3]

//定义元组
let x: [string, number];
x = ['hello', 10]; // OK
x = [10, 'hello']; // Error

//创建枚举
enum Color {Red = 1, Green, Blue}//默认情况下，从0开始为元素编号。 你也可以手动的指定成员的数值。
let c: Color = Color.Green;
let colorName: string = Color[2];
console.log(colorName);  // 通过数值查找名字，显示'Green'因为上面代码里它的值是2

//创建ang对象
let notSure: any = 4;
notSure = "maybe a string instead";
notSure = false; // okay, definitely a boolean

//定义void
let unusable: void = undefined;

//never
//never类型是那些总是会抛出异常或根本就不会有返回值的函数表达式或箭头函数表达式的返回值类型； 变量也可能是 never类型，当它们被永不为真的类型保护所约束时。
function error(message: string): never {
    throw new Error(message);
}

//object
declare function create(o: object | null): void;
create({ prop: 0 }); // OK
create(null); // OK
create(42); // Error
create("string"); // Error
create(false); // Error
create(undefined); // Error



```

###### 类型断言

类型断言好比其它语言里的类型转换，但是不进行特殊的数据检查和解构。 它没有运行时的影响，只是在编译阶段起作用。 TypeScript会假设你，程序员，已经进行了必须的检查。

```typescript
let someValue: any = "this is a string";
let strLength: number = (<string>someValue).length;//使用<>

let someValue: any = "this is a string";
let strLength: number = (someValue as string).length;//使用as
```

###### 一些特点

* 默认情况下`null`和`undefined`是所有类型的子类型。是说你可以把 `null`和`undefined`赋值给`number`类型的变量。
* `never`类型是任何类型的子类型，也可以赋值给任何类型；然而，*没有*类型是`never`的子类型或可以赋值给`never`类型（除了`never`本身之外）。

#### 类型声明

在函数声明的时候标注将使用变量的类型

```typescript
function greeter(person: string) {
    return "Hello, " + person;
}

let user = "man";

document.body.innerHTML = greeter(user);
```

#### 接口 interface

定义一种数据结构，它可以去声明一个变量，也可以作为类型检验。接口可以是类接口，函数接口，索引接口。

> interface ：描述一个对象的取值规范

- 属性接口

```typescript
interface User {
  name: string;
  age?: number;
}

const user1: User = {
  name: "lili",
  age: 18
};
```

- 函数接口

```typescript
interface SearchFunc {
  (source: string, subString: string): boolean;
}

let myFunc: SearchFunc = function(source: string, subString: string){
    ...
    return true;
}
```

- 类接口

```typescript
interface User {
  name: string;
  age?: number;
}
  
class user1 implements User{
    name='zhangsan'  
}
```



#### 类

###### 一般形式

他与js的区别在于在声明变量的时候需要指明数据类型

```typescript
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello, " + this.greeting;
    }
}

let greeter = new Greeter("world");
```

###### 继承

还可以通过继承来拓展类。通过extends关键字来进行继承。 派生类包含了一个构造函数，它 *必须*调用 `super()`，它会执行基类的构造函数。 而且，在构造函数里访问 `this`的属性之前，我们 *一定*要调用 `super()`。  

```typescript
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
    move(distanceInMeters = 45) {//重写基类方法
        console.log("Galloping...");
        super.move(distanceInMeters);//调用基类方法
    }
}

let sam = new Snake("Sammy the Python");
let tom: Animal = new Horse("Tommy the Palomino");

sam.move();
tom.move(34);
```

###### 修饰符

修饰符在ts中也是支持的。

```

```

###### 存取器

###### 静态属性

###### 抽象类

#### 函数

在ts中，函数也可以定义类型

```javascript
function add(x: number, y: number): number {
    return x + y;
}

let myAdd = function(x: number, y: number): number { return x + y; };
```

 有时，你想同时操作多个参数，或者你并不知道会有多少参数传递进来。 在JavaScript里，你可以使用 `arguments`来访问所有传入的参数。 

```javascript
function buildName(firstName: string, ...restOfName: string[]) {
  return firstName + " " + restOfName.join(" ");
}

let employeeName = buildName("Joseph", "Samuel", "Lucas", "MacKinzie");
```



#### 泛型

使用`泛型`来创建可重用的组件，一个组件可以支持多种类型的数据。 这样用户就可以以自己的数据类型来使用组件。 

```javascript
//当不使用泛型时
function identity(arg: any): any {
    return arg;
}
/*
使用any类型会导致这个函数可以接收任何类型的arg参数，这样就丢失了一些信息：传入的类型与返回的类型应该是相同的。如果我们传入一个数字，我们只知道任何类型的值都有可能被返回。

因此，我们需要一种方法使返回值的类型与传入参数的类型是相同的。 这里，我们使用了类型变量，它是一种特殊的变量，只用于表示类型而不是值。
*/

//使用泛型声明的函数
function identity<T>(arg: T): T {//T可以为number等任何数据类型
    return arg;
}

//使用泛型函数
let output = identity<string>("myString");
let output = identity("myString");//编辑器会自动查询myString的类型，然后做声明的


```





#### 注解（装饰器）

 *装饰器*是一种特殊类型的声明，它能够被附加到[类声明](https://www.tslang.cn/docs/handbook/decorators.html#class-decorators)，[方法](https://www.tslang.cn/docs/handbook/decorators.html#method-decorators)， [访问符](https://www.tslang.cn/docs/handbook/decorators.html#accessor-decorators)，[属性](https://www.tslang.cn/docs/handbook/decorators.html#property-decorators)或[参数](https://www.tslang.cn/docs/handbook/decorators.html#parameter-decorators)上。 装饰器使用 `@expression`这种形式，`expression`求值后必须为一个函数，它会在运行时被调用，被装饰的声明信息做为参数传入。 

```javascript
//定义一个注解
//应用于属性
export function Emoji(){//为声明的变量前面加一个hello
    return (target:object,key:string)=>{
        //tar为使用该注解的类
        //key为使用该注解定义变量的变量名
        let val=target[key]
        const getter=()=>{
            return val;
        }
        const setter=(value:string)=>{
            val=`hello${value}`
        }
        Object.defineProperty(target,key,{
            get:getter,
            set:setter,
            enumerable:true,
            configurable:true
        })
    }
}



//使用
@Emoji aaa='boy'

{{result}}   //hello boy
```

