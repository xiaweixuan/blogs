## js基本内容

#### js的组成

js由es、dom、bom组成

ECMAScript（ 由国际标准化组织 ECMA组织制定 ） 只用来标准化 JavaScript 这种语言的基本语法结构，与部署环境相关的标准都由其他标准规定，比如 DOM 的标准就是由 W3C组织（World Wide Web Consortium）制定的。

#### js的发展

（如有误，望联系更改）

=>作为脚本语言用于为表单添加交互效果，

=>渐渐完善可完全操作浏览器页面，

=>随着页面的复杂程度加大，为了简化操作，便开始有了各类第三方库，(jq，reactjs)

=>随后与html、css便开始组件化，可直接使用包装好的组件用于布局，（bootstrap、ant）

=>node的问世使得js可用于编写后端程序，(nodejs)

=>随后js在前端也开始使用node进行工程化开发SPA应用，(通过webpack将前端代码封装打包)

=>渐渐开始模块化用于前后端，前后端的第三方模块开始丰富（也有共用模块）

=>自此js在前后端分别各自发展（独立而又相互关联）

* 补：组件化与模块化其实并不矛盾，组件化主要指的是呈现方面的开发方式，而模块化指功能闪的开发方式。组件化多用于说前端的组件，模块化多用于说后端为了实现某个功能的模块。

* 补：nodejs是有c++编写的，它封装了众多api，使得用js语言就可以完成服务端功能的编写。

* 补：服务端开发的三层结构为web层，service层，dao层。nodejs提供了基本的操作命令方法，而后又有了各种在nodejs上进行封装的各种框架。 koa，express等web框架，bearcat的服务管理框架，sequlize，onela对数据库处理的orm框架。 

#### js设计缺陷

###### 全局变量难以控制

任何函数中都可以生成全局变量，加大了程序的复杂性，容易造成变量污染。

###### 尾行自动加入分号

例如如下代码，将会但会undefined。故return不能直接换行写内容。

```javascript
function(){
    return 
    	{i=1}
}
```

###### 加号运算符

+即可以作为连接符，也可以作为加号使用。如果被操作的数据类型不同会进行自动转化，加剧了运算的复杂性。

###### 数组和对象的区分

由于数组也是对象，所以要区分是数组还是对象需要如下代码

```javascript
if(
	arr &&
	typeof arr === 'object' &&
    typeof arr.length === 'number' &&
    !arr.propertyIsEnumerable('length')
){
	//是数组
}
```

###### 基本数据类型的包装对象

 Javascript有三种基本数据类型：字符串、数字和布尔值。它们都有相应的建构函数，可以生成字符串对象、数字对象和布尔值对象。 

```javascript
alert( typeof 1234); // number
alert( typeof new Number(1234)); // object
```

容易造成混淆

#### js模块化发展

渐渐js开始发展到服务器端，而作为服务端语言，所必备的一个条件就是模块化，于是各个社区开始指定js相关的模块规范。而到es6的时候，es官方也给出了模块机制。

###### 模块化演变

全局function模式==>namespace模式==>IIFE模式==>IIFE模式增强==>模块模式

* 全局function模式

```javascript
function m1(){
  //...
}
```

问题：污染全局命名空间, 容易引起命名冲突或数据不安全，而且模块成员之间看不出直接关系

* namespace模式

```javascript
let myModule = {
  data: 'www.baidu.com',
  foo() {
    console.log(`foo() ${this.data}`)
  },
  bar() {
    console.log(`bar() ${this.data}`)
  }
}
myModule.data = 'other data' //能直接修改模块内部的数据
myModule.foo() // foo() other data
```

 问题: 数据不安全(外部可以直接修改模块内部的数据) 

* IIFE模式：匿名函数自调用(闭包)

```html
// module.js文件
(function(window) {
  let data = 'www.baidu.com'
  //操作数据的函数
  function foo() {
    //用于暴露有函数
    console.log(`foo() ${data}`)
  }
  function bar() {
    //用于暴露有函数
    console.log(`bar() ${data}`)
    otherFun() //内部调用
  }
  function otherFun() {
    //内部私有的函数
    console.log('otherFun()')
  }
  //暴露行为
  window.myModule = { foo, bar } //ES6写法
})(window)


// index.html文件
<script type="text/javascript" src="module.js"></script>
<script type="text/javascript">
    myModule.foo()
    myModule.bar()
    console.log(myModule.data) //undefined 不能访问模块内部数据
    myModule.data = 'xxxx' //不是修改的模块内部的data
    myModule.foo() //没有改变
</script>
```

 问题:无法在该模块引入另外一个模块

* IIFE模式增强 : 引入依赖

```html
// module.js文件
(function(window, $) {
  let data = 'www.baidu.com'
  //操作数据的函数
  function foo() {
    //用于暴露有函数
    console.log(`foo() ${data}`)
    $('body').css('background', 'red')
  }
  function bar() {
    //用于暴露有函数
    console.log(`bar() ${data}`)
    otherFun() //内部调用
  }
  function otherFun() {
    //内部私有的函数
    console.log('otherFun()')
  }
  //暴露行为
  window.myModule = { foo, bar }
})(window, jQuery)

// index.html文件
<!-- 引入的js必须有一定顺序 -->
<script type="text/javascript" src="jquery-1.10.1.js"></script>
<script type="text/javascript" src="module.js"></script>
<script type="text/javascript">
  myModule.foo()
</script>
```

问题：过多的script标签导致请求过多、依赖模糊（不清楚谁依赖谁）、  难以维护 。所以在此基础上，js便开始了产生了模块化的规范。

###### 常见的模块规范

AMD（require.js库使用）非同步

```javascript
//定义没有依赖的模块
define(function(){
   return 模块
})

//定义有依赖的模块
define(['module1', 'module2'], function(m1, m2){
   return 模块
})

//引入使用模块
require(['module1', 'module2'], function(m1, m2){
   //使用m1/m2
})
```

CMD（sea.js库使用）异步

```javascript
//定义没有依赖的模块
define(function(require, exports, module){
  exports.xxx = value
  module.exports = value
})

//定义有依赖的模块
define(function(require, exports, module){
  //引入依赖模块(同步)
  var module2 = require('./module2')
    //引入依赖模块(异步)
    require.async('./module3', function (m3) {
    })
  //暴露模块
  exports.xxx = value
})

//引入使用模块
define(function (require) {
  var m1 = require('./module1')
  var m4 = require('./module4')
  m1.show()
  m4.show()
})
```

CommonJs （node.js中使用）同步

```javascript
//一个一个 导出
module.exports.foo = function(){}
module.exports.age = 1
exports.a = 'hello' //使用exports导出后不能修改此变量，作为静态变量
 
//整体导出，不能使用exports导出
module.exports = { age: 1, a: 'hello', foo:function(){} }


//导入
const foo = require('./foo.js');
```

es6 module （react等框架）异步

```javascript
//导出
export { name1, name2, …, nameN }; // 与之前声明的变量名绑定 命名导出
export default expression; // 默认导出
                                 
//导入                            
import defaultExport from 'module';     
//与commonjs的区别
//1、CommonJs导出的是变量的一份拷贝，ES6 Module导出的是变量的绑定（引用）；
//2、CommonJs是单个值导出，ES6 Module可以导出多个；
//3、CommonJs是动态语法可以写在判断里，ES6 Module静态语法只能写在顶层；
//4、CommonJs的 this 是当前模块，ES6 Module的 this 是 undefined。
```

###### 以及组合型的模块规范

UMD通用模块 （ 就是AMD+CommonJs+全局变量的组合规范 ）

```javascript
(function (global, factory) {
    typeof exports === 'object' && typeof module !== 'undefined' ? module.exports = factory() :
    typeof define === 'function' && define.amd ? define(factory) :
    (global.libName = factory());//在原生js情况下，此处的global就是指的window对象
}(this, (function () { 'use strict';})));
```

在实际的情况中，各个类库基本都是将某个规范细微改动后使用，例如node也是将commonjs的模块机制调动后使用的。react也是将自行编译了es6的模块机制。

| 语法       | commonJS                             | ES6                                     | AMD                                                          | CMD                                                          |
| ---------- | ------------------------------------ | --------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 导出       | module.exports = {}<br/>exports = {} | export default {}<br/>export var a = 10 | define(id?: String, dependencies?: String[], factory: Function(Object); | define(function(require, exports, module) {});               |
| 导入       | require(‘module’)                    | import module from ‘module’             | require([‘myModule’], function(myModule) {});                | var a = require(‘./a’);<br/>require.async(‘./b’, function(b) {}); |
| 加载       | 动态 – 同步                          | 静态                                    | 动态-异步                                                    | 动态-同步或异步                                              |
| 导出的对象 | 副本                                 | 引用                                    |                                                              |                                                              |
| 多次导出   | 同一个对象                           |                                         |                                                              |                                                              |
| 模块作用域 | 文件                                 | 文件                                    | 文件                                                         |                                                              |
| 循环加载时 | 加载已执行的部分                     |                                         |                                                              |                                                              |
| 浏览器支持 | 是                                   | 否                                      | 是                                                           | 是                                                           |
| node支持   | 是                                   | 否                                      |                                                              | 是                                                           |
| 典型环境   | nodejs                               | babel，vue                              | requirejs                                                    | seajs                                                        |
| 引用目录时 | 找package.json定义的main,或index     |                                         |                                                              |                                                              |

#### js的异步发展

js是单线程，也就是所有的工作将按顺序完成，这也就是正常的同步执行，但是如果有一个任务加载时间较长，后面的任务将始终得不到加载，即发生堵塞，所以，异步执行对js的意义非凡。

此处所讲的，也可是说是es的异步发展史。除此之外，仍有一些模式可以解决异步问题。

 JS的异步发展史，可以认为是从 callback -> promise -> generator -> async/await。 

###### 回调函数callback

被作为实参传入另一函数，并在该外部函数内被调用，用以来完成某些任务的函数。如setTimeOut，ajax请求，readFile等。



```jsx
function greeting(name){
  console.log("Hello,"+name);
}
function processUserInput(callback){
  let name = prompt("请输入你的名字");
  callback(name);
}
processUserInput(greeting);
```

优点：
 解决了异步的问题。
 缺点：
 回调地狱：多个回调函数嵌套的情况，使代码看起来很混乱，不易于维护。

###### Promise

Promise是es6提出的异步编程的一种解决方案。
Promise 对象有三种状态：

- pending: 初始状态，既不是成功，也不是失败状态。
- fulfilled: 意味着操作成功完成。
- rejected: 意味着操作失败。
   promise的状态只能从pending变成fulfilled，和pending变成rejected，状态一旦改变，就不会再改变，且只有异步操作的结果才能改变promise的状态。
   例：



```tsx
function read(url){
  return new Promise((resolve,reject) => {
    fs.readFile(url,'utf-8',(err, data) => {
      if(err) reject(err);
      resolve(data);
    })
  })
}
read(A).then(data => {
  return read(B);
}).catch(reason => {
  console.log(reason);
})
```

优点：解决了回调地狱的问题，将异步操作以同步操作的流程表达出来。
缺点：无法取消promise。如果不设置回调函数，Promise内部抛出的错误，不会反应到外部。当处于Pending状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。当执行多个Promise时，一堆then看起来也很不友好。

###### Generator

Generator是es6提出的另一种异步编程解决方案，需要在函数名之前加一个*号，函数内部使用yield语句。Generaotr函数会返回一个遍历器，可以进行遍历操作执行每个中断点yield。



```jsx
function *count(){
  yield 1;
  yield 2;
  return 3;
}
let c = count();
console.log(c.next());  // { value: 1, done: false }
console.log(c.next()); // { value: 2, done: false }
console.log(c.next()); // { value: 3, done: true }
console.log(c.next()); // { value: undefined, done: true }
```

优点：没有了Promise的一堆then(),异步操作更像同步操作，代码更加清晰。
 缺点：不能自动执行异步操作，需要写多个next()方法，需要配合使用Thunk函数和Co模块才能做到自动执行。

###### async/await

`async function`自动将常规函数转换成Promise，返回值也是一个Promise对象, 只有async函数内部的异步操作执行完，才会执行then方法指定的回调函数, 异步函数内部可以使用await.

`await` 放置在Promise调用之前，await 强制后面点代码等待，直到Promise对象resolve，得到resolve的值作为await表达式的运算结果  

```javascript
//实例一
async function read() {
 let readA = await readFile('data/a.txt')  //readFile是异步发开文件
 console.log(readA)
}
read()
//实例二
function pm(){
    return new Promise((res,rej)=>{
        res('1')
    })
}
async function test(){
    let a=await pm();
    let b=await `2`;
    return a+b;
}
test()  //12
```

优点：内置执行器，比Generator操作更简单。async/await比*/yield语义更清晰。返回值是Promise对象，可以用then指定下一步操作。代码更整洁。可以捕获同步和异步的错误。
 缺点：暂时没有人提及这种写法的缺点，目前前端圈一致推荐的异步操作的写法。

###### 总结

上图中列出了好几条时间线，但他们并不是说js发展的三个方向，第一条是在说js这门语言用途的发展线，第二条，则是将js再写功能时，如何写功能，如何将功能变得更加独立的方法，这个方法的发展线，第三条线是指，为了解决js异步使用，在异步这个操作上的发展线。三条线不可混为一谈，是js在三个方面的不同发展状况。

至此我们主要介绍了js的发展，为我们明确了js能够做什么。然后等待着我们学习的就是js的一些语法操作，包括es语法、dom操作、bom操作、js进阶。当着写基础内容学习完成后，已经可以通过js做一些页面效果了，即可进入下一章设计模式的学习，来了解学习js更加底层的东西。



## js的设计模式

在我们了解了js的用途、语法操作之后，我们已经可以使用js进行开发了。那当我们使用js开发一个功能时，常常会需要引用其他的功能库，可如果我们要自行开发，该如何做呢？例如我们将做一个大则jq小则animate.js这样的第三方库，我们该了解哪些东西呢？

#### 做一个第三方库（模块）

###### 什么叫第三方库，什么叫模块

我们想完成一个功能，为了实现复用且降低耦合性，我们将实现他的函数抽离出来，封装到一个js文件中，当使用到他的时候，再引入该js文件，而这个js文件就可以叫做一个第三方库。以模块化思想来讲，这可以将它叫做一个模块（模块和第三方库的封装上仍有差异），当然此时该模块的功能有很多，我们还可以再将它细分为很多模块（即一个模块可以由很多其他的模块组成）。

例如我们常用的jquery.js，这是一个第三方库（也有模块化jq），通过把他引入到我们的项目中，可以使用它封装的函数以达成某种效果，例如淡入淡出。但有时候如果对某个效果的要求很细致，例如几秒内淡入，每毫秒透明度变化多少，这些东西我们并不能很好的设置。所以为了实现这种效果，我们只有自行的封装这个功能。

而要编写一个第三方库或者模块内容很多，首先我们需要了解的是设计模式。

###### 设计模式

设计模式常常是为解决某种问题而设计的一种特定思维。例如如何把大象塞进冰箱？我们通常就是想到的就是三步，开门、塞象、关门。他已经成了一种固定思维，用这种方法一定可以完成，那这就可以叫做关大象模式。当你关长颈鹿、关猩猩的时候，仍然是这三步，因为这已经是一种固定的模式了。

设计模式即使如此，他为我们提供了一种解决问题的方式，常见的设计模式有，工厂模式、单例模式、中介者模式、观察者模式、外观模式、装饰者模式等。

######                                                                                                   开始封装一个库

首先我们要明确我们的库是做什么用的，此处我们做的是cvsA.js。有了他可以直接为你的canvas增添几种特定的动画效果。例如流星雨、滑动烟花特效等。

我们使用umd规范，因为使用它可以更好的兼容不同环境下的项目。

```javascript
(function (global, factory) {
    typeof exports === 'object' && typeof module !== 'undefined' ? module.exports = factory() :
    typeof define === 'function' && define.amd ? define(factory) :
    (global.libName = factory());//在原生js情况下，此处的global就是指的window对象
}(this, (function () { 
    'use strict';
	//内容
})));
```

未完待续。。。





## web应用的模式

#### mv*

###### MVC

例如react属于mvc中的v，而常常将mc放到后端进行。

react以组件化思想更方便的构建View。

react的实现。。

未完待续。。。

###### mvp

###### mvvm

