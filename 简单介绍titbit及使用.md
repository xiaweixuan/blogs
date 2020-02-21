### titbit是什么
titbit是一个于npm中发布的模块，他可以nodejs的web开发框架，仿写的express框架，其功能与特点大致相同

那么什么是web开发框架呢，首先大家应该知道，nodejs可以用于写后端，而后端最简单的任务就是收到客户端浏览器的访问并返回数据。首先，让我们理解一下访问网站的过程。

当我们在浏览器中输入了一个域名，发出请求，找到相应的ip地址，紧接着找到相应的电脑，这里的电脑也就是服务器，服务器接到请求就会返回信息，或者将一个html页面返回给浏览器，浏览器再经过渲染，最终形成了我们看到的页面。

那么，我们首先写一个最简单的nodejs服务器
```javascript
var http=require('http');
http.createServer((req,res)=>{
    res.end('访问成功');
}).listen(8002);
```
这就是最简单的一个服务器，他监听了8002端口，每当有数据通过此接口传入的时候，他就会获取并返回数据。
当然，这里我们使用的是http模块，也是nodejs自带的模块，并不需要下载。但是，这只是最简单的并没有什么卵用的一个demo。如果我们要实现，返回各种各样的数据，文件什么的怎么办呢？首先大家要明白，我们是可以实现的，这可能会涉及更多其他的底层模块，但是缺点是，引入的模块多，代码量长。所以，就出现了titbit模块，他将这一切封装了起来，想要做复杂的操作，你只需要引用他的api即可。
比如说以上的代码换做titbit可以这样实现
```javascript
var app =new titbit();
var {router}=app;
router.get('/',async c => {
    c.res.body='访问成功';
});
app.run(8002,'localhost');
```
这里并不是用了其他方法，他只不过是将http.createServer()封装到他自己的模块中，不需要我们自己再去创建，当你new一个titbit对象时，他就已经执行了http.createServer()。
当然，由于操作简单，我们可能并不容易发现他的简便，但当代码量变多时，你就可以发现他的简介与思路的清晰了。接下来，我们再来介绍一下他的其他作用。
### titbit常用功能
###### 获取参数
经过一层层封装，最终titbit将git、post等方法放到了titbit.prototype.router中，所以我们先设置变量
```javascript
var app=new titbit();
var r=app.router;
r.get('/q',async c=>{
	c.res.body=c.query;
})
```
url的？后面为参数，它会自动解析到c.query
c.res.body是返回的数据
这段代码的意思是，当url下一级为q是，将参数再返回给网页
例如我访问
https://laoxia.xinxindeyuming.cn/q?a=1
那么返回到我浏览器的是{"a":"1"}

其他功能详见[王勇git](https://github.com/master-genius/titbit)