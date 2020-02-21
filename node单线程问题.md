###### 单线程的问题

由于node的单线程，那么自然存在两个常见问题：如何充分利用多核cpu服务器（单进程只会在一个核上进行处理）、如何保证进程健壮性（单线程导致爆出的异常没有被捕捉就会引起整个进程的崩溃）。

 ==多进程架构==

解决思路为，让父程序创建n个相同子程序，子程序皆为一个个的web服务且监听相同端口。父组件称为主线程，子进程为工作进程。

问题：父子组件中如何通讯？

回答：主进程与线程之间通过onmessafe和postMessage进行通讯，工作进程对象则通过send实现主进程想子进程发送数据。通讯的原理为在创建子进程时，会创建IPC通道

问题：n个相同子进程怎么监听同一个端口？

回答：在主进程中创建server=http.createServer，然后通过句柄传递将server传递给各个子进程。句柄传递可支持传递net.Socket、net.Server、net.Native、dgram.Socket、dgram.Native

```javascript
//parent.js
var cp=require("child_process");
var child1=cp.fork('child.js');
var child2=cp.fork('child.js');
var server =require('net').createServer();
server.listen(1337,function(){
    child1.send('server',server);//发送句柄
    child2.send('server',server);
    server.close();
})
//child.js
var http=require('http');
var server=http.createServer(function(req,res){
    res.writeHead(200,{'Content-Type':'text/plain'});
    res.end(process.pid);
})
process.on('message',function(m,tcp){//通过事件接收
    if(m==='server'){
        tcp.on('connection',function(socket){
            server.emit('connection',socket);
        })
    }
})

```



==优化多进程==

*进程事件*

对于子进程对象，除了send方法、message方法，还有以下方法：

* error 当子进程无法被复制创建、无法被杀死、发送消息时会触发
* exit 子进程退出时触发的事件
* close 子进程的标准输入输出流终止时
* disconnect在父进程或子进程中调用disconnect方法时触发

*自动重启*

我们通过画饼图曝光租户不出个不过的exit事件来获知其退出信息并重启

*负载均衡*

node默认提供的机制时采用操作系统的抢占式策略。但是对于node而言，需要分清楚的时她的繁忙由CPU、I/O两个方面构成，影响抢占的是cpu繁忙度。对于i/o繁忙，cpu空闲的情况，可能造成某个进程抢到较多的请求，形成负载不均衡的情况。

==cluster模块==

解决多核cpu的利用率问题

```javascript
//cluster.js  使worker.js根据核数创建相对应的进程
var cluster=require('cluster');
cluster.setupMaster({
    exec:"worker.js"
});
var cpus=require("os").cpus();
for(var i=0;i<cpus.length;i++){
    cluster.fork(); 
}
```