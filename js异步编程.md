###### js异步编程

==高阶函数==

将函数作为参数或者将函数作为返回值的函数。高阶函数的例子

```
emmitter.on('data',function(){})
```

==偏函数==

为了调用函数A，但为了应对不同情景，需要对函数A根据不同场景做出细微改变，于是创建创建函数B去返回根据不同场景更改后的函数A（函数A的参数或者变量是预置的），函数B即为偏函数

```javascript
//一个场景
var toString=Object.prototype.toString;
var isString=function(obj){
    return toString.call(obj)== '[object String]'
}
var isFunction=function(){
    return toString.call(obj)== '[object Function]'
}
//如果有很多类似的isXX的方法，我们一一写出来，会出现更多的代码冗余，所以为解决这个问题，我们构造新函数
var isType=function(type){
    return function(obj){
        return toString.call(obj)== '[object '+type+']';
    }
}
var isString=isType('String');
var isFunction=isType('Function');

//常见的偏函数，例如Underscore提供的after方法。生成一个需要调用多次才真正执行的实际函数。
_.after=function(timers,func){
    if(timers<=0) return func();
    return function(){
        if(--timers<1) {return func.apply(this,arguments)}
    }
}

```

 ==异步编程==

node的非阻塞I/O可以使得cpu与I/O不相互依赖等待，从而让资源得到更好的的利用。但与此同时，异步编程又会产生很多麻烦，例如异常处理、函数嵌套过深、阻塞代码、多线程编程、异步转同步等。对于这一系列的问题，异步编程的解决方案有以下三种：1、事件发布/订阅者模式。2、Promise/Deferred模式。3、流程控制库。

*事件发布/订阅者模式*

node中的events模块就是对发布订阅模式的简单实现。

```javascript  
//订阅
emmiter.on('e1',(msg)=>{console.log(msg)})
//发布
emmiter.emit('e1','hello')
```

node中很多模块都继承了events对象，例如http模块，我们常用的res.on('data',()=>{})，这让我们吧注意力放在data这个业务事件点上即可，无需关心内部流程。

如下多层嵌套问题

```javascript
//问题代码，由于是异步，所以要想让代码先后执行需要将其嵌套入回调函数中，故造嵌套过深
fs.readdir(path.join(__dirname,'..'),function(err,files){
    files.forEach(function(filename,index){
        fs.readFile(filename,'utf8',function(err,file){})
    })
})
//以异步思想编程，以渲染页面所需的模板读取、数据读取和本地化为例介绍
var count=0;
var results={};
var done=function(key,value){
    results[key]=value;
    count++;
	if(count===3){
        render(results);//等三个方法全部执行完成以后，需要的数据准备齐全以后再执行渲染功能
    }
}
fs.readFile(template_path,'utf8',function(err,template){
    done('template',template)
})
db.query(sql,function(err,data){
    done('data',data)
})
l10n.get(function(err,res){
    done('res',res)
})
//由于多个场景中并不能确保哪个异步函数可以先执行，所以需要借助一个第三变量（用于检测是第几次执行）和第三函数来处理异协作的结果通常把它叫做哨兵变量。利用偏函数处理哨兵变量和这个第三函数。
var after=function(times,callback){
    var count=0,results={};
    return function(key,result){
        result[key]=value;
        count++;
        if(count===times){
           callback(results)
        }
    }
}
var done=after(times,render);                                         
var emitter=new events.Emmiter();

var times=3;
function render(){}

emitter.on("done",done);
emitter.on("other",other);//可以拓展其他功能

fs.readFile(template_path,'utf8',function(err,template){
    emitter.emit("done",'template',template)
})
db.query(sql,function(err,data){
    emitter.emit("done",'data',data)
})
l10n.get(function(err,res){
    emitter.emit("done",'res',res)
})

```



*Promise/Deferred模式*

commonjs草案目前已经抽象除了promise/A、promise/B、promise/D等经典模型。我们着重介绍一下p romise/A。

Promise/A对单个异步操作做出了抽象定义

* promise只会处于三种状态中的一种：未完成、完成、失败
* promise的状态只会出现从为完成想完成或者失败转换，不能逆转
* promise的状态一旦转换，将不能被更改

一个Promise只要具备一个then方法即可，但对then方法有如下要求

* 接受完成状态、错误状态的回调方法
* 可选的支持progress事件作为第三个方法
* then方法只接受function对象，其他对象皆被忽略
* then方法继续返回promise对象实现链式调用

接下来我们自己封装一个Promise对象和Deferred对象

```javascript
var Promise=function(){
    EventEmitter.call(this)
}
util.inherits(Promise,EventEmitter)
Promise.prototype.then(fuc1,fuc2,fuc3){
    if(typeof fuc1==='function'){
        this.once('success',fuc1)
    }
    if(typeof fuc2==='function'){
        this.once('error',func2)
    }
    if(typeof fuc3==='function'){
        this.once('progress',fuc3)
    }
    return this;
}

var Deferred=function(){
    this.state='unfulfilled';
    this.promise=new Promise;
}
Deferred.prototype.resolve=function(obj){
    this.state='fulfilled';
    this.promise.emit('success',obj)
}
Deferred.prototype.reject=function(err){
    this.state='failed';
    this.promise.emit('error',err)
}
Deferred.prototype.resolve=function(data){
    this.promise.emit('progress',data)
}

```

封装完成以后，我用他来对一个相应对象进行封装

```javascript
//原来的形式
res.on('data',function(chunk){
    console.log(chunk)
})
res.on('end',function(){
    //done
})
res.on('error',function(err){
	//error
})
//我们将上述代码可以转化为一下形式
res.then(function(){
    //done
},function(err){
    //error
},function(chunk){
    console.log(chunk)
})
//为了完成以上形式，我们利用promise与deferred来将res的操作进行封装新的api
var pro=function(res){
    var deferred=new Deferred();
    var result='';
    res.on('data',function(chunk){
        result+=chunk;
        deferred.progress(chunk)
    })
    res.on('end',function(){
    	deferred.resolve()
	})
	res.on('error',function(err){
		deferred.reject(err)
	})
    return deferred.promise;
}
//回调示例
pro(res).then(function(){
    //done
},function(err){
    //error
},function(chunk){
    console.log(chunk)
})
```

上面可以看出promise主要作用于外部，通过then方法暴露给外面以添加自定义逻辑。deferred主要是用于内部维护异步模型的状态。

Q模块是Promise/A规范的一个实现，可使用npm进行下载。使用q模块对fs.readFile进行封装

```javascript
var readFile=function(file,encoding){
    var deferred=Q.defer();
    fs.readFile(file,encoding,deferred.makeNodeResolver())
    return deferres.promise
}
readFile('foo.txt','utf8').then(function(data){
    //success
},function(err){
    //err
})
```

综上，promise模式比原始模式的事件监听和触发更具优美，但缺陷是需要为不同的场景封装不同的api。

*流程控制库*

上面的两种都是经典的模式或者写入规则里的，接下来将介绍一些非模式化的应用。

==尾触发==

最常见的地方就是中间件。在创建createServer的时候创建一个空栈，每次使用use方法的时候将中间件放进队列。

==async==

async模块介绍了20多种方法处理异步，这里我们介绍典型的几种。

async.series方法，异步的串行执行

```javascript
async.series([
    function(callback){
        fs.readFile('a1.txt','utf8',callback)
    },
    function(callback){
        fs.readFile('a2.txt','utf8',callback)
    }
],function(err,result){
	//result为一个数组，结果为上面每个函数调用后的结果
})



//以上等同于如下
fs.readFile('a1.txt','utf8',function(err,content){
    if(err) return callback(err)
    fs.readFile('a2.txt','utf8',function(err,data){
        if(err) return callback(err)
        callback(null,[content,data])
    })
})
```

result为一个数组，结果为上面每个函数调用后的结果，这里面传入的callback（）并非由使用者指定。

async.parallel异步的并行执行

```javascript
async.parallel([
    function(callback){
        fs.readFile('a1.txt','utf8',callback)
    },
    function(callback){
        fs.readFile('a1.txt','utf8',callback)
    }
],function(err,result){
	//result为一个数组，结果为上面每个函数调用后的结果
})


//以上等同于如下
fs.readFile('a1.txt','utf8',function(err,content){
    if(err) return callback(err)
    done(1,content)
})
fs.readFile('a2.txt','utf8',function(err,data){
    if(err) return callback(err)
    done(2,data)
})
```