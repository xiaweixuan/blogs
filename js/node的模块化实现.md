###### node的模块化实现

==CommonJS规范的提出==

为了使得js不只在浏览器上运行，社区也开始为js指定了相应的规范，其中commonJS规范的提出算是最重要的一个里程碑。自此，Node与浏览器、W3C组织、CommonJS组织、ES（基本语法）共同构成了一个繁荣的生态系统。

* w3c指定了dom与bom规范

* commonjs规范涵盖了模块、二进制、buffer、字符集编码、I/O流、进程环境、套接字、单元测试、web服务器网关接口、包管理等。

* 浏览器中可以使得w3c所指定的规范及es语法

* node环境下可以使用commonjs所指定的规范以及es语法

==CommonJS模块的规范==

* 通过require接受模块标识，引入一个模块的api进入当前上下文

* ```javascript
  require("math")
  ```

* 上下文提供一个exports对象用于导出当前模块的方法和变量。在模块中存在一个module对象代表模块本身，而exports是module的属性。在node中一个文件就是一个模块。

* ```javascript
  //math.js
  exports.add=()=>{}
  //main.js
  var math = require("math")
  math.add()
  ```

==node模块的实现==

node在实现模块时对其进行了一定的取舍，也增加了不少自己的特性。node在引入模块时需要进行一下三个步骤1、路径分析。2、文件定位。3、编译执行。

在node中自身提供的模块交核心模块，用户编写的为文件模块。（部分核心模块在node源码编译过程中编译进了二进制执行文件，在nodej进程启动时，便加载入了内存，所以这部分模块可以省略文件定位和编译执行的阶段）

当定位到一个模块后，根据路径载入并编译。每一个文件模块都是一个对象，他的定义如下

```
function Moudle(id,parent){
this.id=id;
this.exports={};
this.parent=parent;
if(parent&&parent.children){parent.children.push(this)}
this.filename=null;
this.loaded=false;
this.children=[];
}
```

在commonjs中每个模块文件中都存在require、exports、module三个变量，但是在模块文件中并没有这么定义。事实在编译的过程中node会对js文件内容进行包装如下：

```javascript
(function(exports,require,module,__filename__dirname){/*js文件内容*/})
```

这样每个模块之间都进行了作用域隔离，包装后通过vm原生模块的runInThisContext()方法执行（类似eval方法，知识具有明确的上下文），返回一个具体的function对象，将当前模块对象的exports，require，module（模块本身）以及文件定位作为参数 传给function()执行。之后，exports属性被返回给调用方，其上面的任何方法属性都可以被外部调用，但是模块中的其余变量或者属性则不可以被直接调用。

此外，还有一个问题，在存在exports的情况下，为何存在module.exports？如下示例

```javascript
var change=function(a){
    a=100;
    console.log(a)//100
}

var a=10;
change(a);
console.log(a)//10
```

exports对象使用过形参的方式传入的，直接赋值形参会改变形参的引用，但并不能改变作用域外的值。所以如果要达到require引入一个类的效果，请复制给module.exports。

==前后端共用模块==

刚刚在谈论的为后端模块的实现，但实则前后端对于模块的引用稍有不同。纵观node的模块引入几乎全是同步的，但这在前端如果使用同步加载则会产生很多问题，例如ui在初始化中会花费更长的时间。

所以commonjs为后端指定的规范并不适用于前端场景，所以便产生了AMD规范与CMD规范。

AMD规范的模块定义如下,模块的id和依赖是可选的

```javascript
//define(id?,dependencies?,factory)
define(['dep1','dep2'],
	function(dep1,dep2){
        var exports={};
        exports.sayHello=function(){}
        return exports;
    }
)
```

CMD规范的模块定义如下,与AMD的区别主要在于定义模块和以来引入的部分，CMD可以动态传入依赖

```javascript
define(function(require,exports,module){})
```
兼容多种规范

```javascript
(function(name,definition){
	var hasDefine=typeof define === 'function',//检查上下文环境是否为AMD或CMD
        hasExports =typeof module!=='undefined' && module.exports;//检查上下文是否为node
    
    if(hasDefine){define(definition)}//amd或者cmd中定义模块
    else if(hasExports){module.exports=definition()}//定义为普通node模块
    else{this[name]=definition()}//将模块的执行结果挂在到window变量中，在浏览器中this指向window对象
})('hello',function(){var hello=function(){};return hello;})
```