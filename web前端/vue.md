# vue知识架构

### 基本语法

##### 引入

直接引入`vue.js`即可使用

```html
<!-- 开发环境版本，包含了有帮助的命令行警告 -->
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

<!-- 生产环境版本，优化了尺寸和速度 -->
<script src="https://cdn.jsdelivr.net/npm/vue"></script>

<!-- 支持原生ES Modules时 -->
<script type="module">
  import Vue from 'https://cdn.jsdelivr.net/npm/vue@2.6.11/dist/vue.esm.browser.js'
</script>
```

也可以通过脚手架创建

```shell
npm install -g @vue/cli

vue create my-project
cd my-project
npm run serve
```

##### 绑定

```html
<!--显示文本-->
<span>Message: {{ msg }}</span>
<!--显示html语句-->
<p>Using v-html directive: <span v-html="rawHtml"></span></p>
```

##### 指令

* v-if：判断
* v-for：循环
* v-bind：绑定元素本身属性，简写为`:`，通常里面夹杂着变量
* v-on：绑定事件,简写为`@`
* v-model：双向绑定，他监测的变会变化
* v-show：改变display属性，会渲染`dom`但不一定会显示

```html
<div :class="[isActive ? activeClass : '', errorClass]"></div>
<div :class="{'content-collapse':collapse}">
    
<i v-if="!collapse" class="el-icon-s-fold"></i>
<i v-else class="el-icon-s-unfold"></i>
    
<input v-model="message">
    
<h1 v-show="false">Hello!</h1>
```

除此之外，部分指令还有修饰符

下面是事件的修饰符

```html
<!-- 阻止单击事件继续传播 -->
<a v-on:click.stop="doThis"></a>

<!-- 提交事件不再重载页面 -->
<form v-on:submit.prevent="onSubmit"></form>

<!-- 修饰符可以串联 -->
<a v-on:click.stop.prevent="doThat"></a>

<!-- 只有修饰符 -->
<form v-on:submit.prevent></form>

<!-- 添加事件监听器时使用事件捕获模式 -->
<!-- 即内部元素触发的事件先在此处理，然后才交由内部元素进行处理 -->
<div v-on:click.capture="doThis">...</div>

<!-- 只当在 event.target 是当前元素自身时触发处理函数 -->
<!-- 即事件不是从内部元素触发的 -->
<div v-on:click.self="doThat">...</div>
```

还有绑定表单时的修饰符

* lazy: 在默认情况下，`v-model` 在每次 `input` 事件触发后将输入框的值与数据进行同步 (除了[上述](https://vuejs.bootcss.com/guide/forms.html#vmodel-ime-tip)输入法组合文字时)。你可以添加 `lazy` 修饰符，从而转变为使用 `change` 事件进行同步： 
* number: 如果想自动将用户的输入值转为数值类型，可以给 `v-model` 添加 `number` 修饰符
* trim: 如果要自动过滤用户输入的首尾空白字符，可以给 `v-model` 添加 `trim` 修饰符 

```jsx
<input v-model.trim="msg">
```





##### 方法、计算、侦听器

methods是我们定义方法的地方，通常是一次性使用的方法，无响应式依赖。

computer，计算属性也是一个一个的方法，但仙姑低于methods而言，计算属性基于内部变量的响应式依赖进行缓存。

##### 组件传值

向子组件传值

```jsx
export default {
	props: ['title'], 
};


{
    <blog-post title="My journey with Vue"></blog-post>
}

```

向父组件传值

```html
<!--父组件监听自定义的事件-->
<blog-post
  ...
  v-on:enlarge-text="postFontSize +=  $event"
></blog-post>
<!--子组件创建一个自定义的事件-->
<button v-on:click="$emit('enlarge-text', 0.1)">
  Enlarge text
</button>
```

##### 生命周期

![Vue 实例生命周期](https://cn.vuejs.org/images/lifecycle.png)

### router

##### 转跳、传参

> ###### 标签转跳
>
> ```javascript
> 1. 不带参数
>  <router-link :to="{name:'home'}"> 
> <router-link :to="{path:'/home'}"> //name,path都行, 建议用name 
> // 注意：router-link中链接如果是'/'开始就是从根路由开始，如果开始不带'/'，则从当前路由开始。
>  2.带参数
>  <router-link :to="{name:'home', params: {id:1}}"> 
> // params传参数 (类似post)
> // 路由配置 path: "/home/:id" 或者 path: "/home:id" 
> // 不配置path ,第一次可请求,刷新页面id会消失
> // 配置path,刷新页面id会保留
> // html 取参 $route.params.id
> // script 取参 this.$route.params.id
> <router-link :to="{name:'home', query: {id:1}}"> 
> // query传参数 (类似get,url后面会显示参数)
> // 路由可不配置
> // html 取参 $route.query.id
> // script 取参 this.$route.query.id
> ```
>
> ##### **push**
>
> ```javascript
> 1. 不带参数
> this.$router.push('/home')
> this.$router.push({name:'home'})
> this.$router.push({path:'/home'})
> 2. query传参 
> this.$router.push({name:'home',query: {id:'1'}})
> this.$router.push({path:'/home',query: {id:'1'}})
> // html 取参 $route.query.id
> // script 取参 this.$route.query.id
> 3. params传参
>  this.$router.push({name:'home',params: {id:'1'}}) // 只能用 name
>  
> // 路由配置 path: "/home/:id" 或者 path: "/home:id" ,
> // 不配置path ,第一次可请求,刷新页面id会消失
> // 配置path,刷新页面id会保留
> // html 取参 $route.params.id
> // script 取参 this.$route.params.id
> 4. query和params区别
> query类似 get, 跳转之后页面 url后面会拼接参数,类似?id=1, 非重要性的可以这样传, 密码之类还是用params刷新页面id还在
>  params类似 post, 跳转之后页面 url后面不会拼接参数 , 但是刷新页面id 会消失
> ```
>
> ##### replace
>
> ```
> 使用this.$router.replace() (用法同上,push)
> ```
>
> ##### go
>
> ```javascript
> this.$router.go(n)
> 向前或者向后跳转n个页面，n可为正整数或负整数
> ```
>
> ##### 区别
>
> * push：跳转到指定路径，并想history栈中添加一个记录，点击后退会返回到上一个页面
>
> * replace：跳转到指定路径，但是history栈中不会有记录，点击返回会跳转到上上个页面 (就是直接替换了当前页面)
>
> * go：向前或者向后跳转n个页面，n可为正整数或负整数
>
> 

##### keep-alive



### vuex

用于状态存储，如果是小型项目可以考虑使用bus



简单介绍下store的使用方法

> 在`main.js`中引入
>
> ```javascript
> import Vue from 'vue'
> import Vuex from 'vuex'
> import store from './store'
> 
> Vuew.use(Vuex)
> new Vue({
>     store
> })
> 
> ```
>
> store定义
>
> * state中是你所需要存储的状态值
> * mutations中是你的逻辑操作
> * actions中是你的业务方法，在他里面去调用逻辑操作
> * getters中是属于计算属性
> * 当我们分模块分别创建多个store的时候将他们最终归入module中
>
> ```javascript
> export default new Vuex.Store({
>   state: {
>       count:0
>   },
>   getters:{
>   	  myCount(state){
>           return `数值是${state.count}`
>       }      
>   },
>   mutations: {
>       increment(state,n){
>           state.count+=n
>       },
>       decrement(state,n){
>           state.count-=n
>       }
>   },
>   actions: {
>       async myIncrement(context,obj){
>           context.commit('increment',2)
>           await const products=[1,2,3]
>           console.log(obj)
>           //其他业务
>           return products
>       },
>       async myDecrement(context){
>         context.commit('increment',2)
>         await const products=[1,2,3]
>         //其他业务
>         return products
>     }
>   },
>   modules: {
>   }
> })
> ```
>
> 在组件中调用
>
> ```javascript
> //调用state值
> import {mapState,mapGetters} from 'vuex'
> computed:{
>     ...mapState(['count'])
>     ...mapGetters(['myCount'])
> }
> {{count}}
> {{myCount}}
> //改变值
> import {mapMutations,mapActions} from 'vuex'
> methods:{
>     ...mapMutations(['increment','decrement'])
> 	...mapActions(['myIncrement','myDecrement']) 
>     async increase(){
>         this.$store.state.count+=1//不推荐
>         this.increment()//直接调用逻辑方法，也不推荐
>         const prodicts=await this.myIncrement({a:1})
>     }
> }
> ```

但通常，我们的目录是比较复杂的

> 在大型项目中。我们的目录通常是这样
>
> ```
> ├── index.html
> ├── main.js
> ├── api
> │   └── ... # 抽取出API请求
> ├── components
> │   ├── common
> │   └── page
> └── store
>     ├── index.js          # 我们组装模块并导出 store 的地方
>     ├── actions.js        # 根级别的 action
>     ├── mutations.js      # 根级别的 mutation
>     └── modules
>         ├── cart.js       # 购物车模块
>         └── products.js   # 产品模块
> ```
>
> 其中每个模块中都包括自己的一个store及其自己的state、getters、action等
>
> 最终合并后
>
> ```javascript
> //index.js
> export default new Vuex.Store({
>     //...
>     module:{
>         
>     }
> })
> //cart.js
> export default const cart={
>     state:{},
>     getters:{},
>     mutations:{},
>     actions:{}
> }
> //调用state值,调用cart模块中的值
> import {mapState,mapGetters} form 'vuex'
> computed:{
>     ...mapState({
>         count:state=>{
>             return state.app.count
>         }
>     })
> }
> {{count}}
> //改变值和之前相同
> ```
>
> 

### 高阶

##### 插槽

基本写法

```html
<navigation-link>
  代替插槽的内容
</navigation-link>

<a>
  <slot>默认内容</slot>
</a>
```

插槽命名

```html
<base-layout>
  <template #header>
    <h1>Here might be a page title</h1>
  </template>

  <p>A paragraph for the main content.</p>
  <p>And another one.</p>

  <template #footer>
    <p>Here's some contact info</p>
  </template>
</base-layout>


<!--组件内容-->
<div class="container">
  <header>
    <slot #default="{header}"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot #default="{footer}"></slot>
  </footer>
</div>
```

调用子组件作用域

```html
<current-user>
  <template v-slot:default="slotProps">
    {{ slotProps.user.firstName }}
  </template>
</current-user>

<span>
  <slot v-bind:user="user">
    {{ user.lastName }}
  </slot>
</span>
```



##### 异步组件&动态组件

在添加keepalive后使得组件被缓存

```html
<!-- 组件会在 `currentTabComponent` 改变时改变 -->
<component v-bind:is="currentTabComponent"></component>
<!-- 失活的组件将会被缓存！-->
<keep-alive>
  <component v-bind:is="currentTabComponent"></component>
</keep-alive>

```



##### 动画过渡

子组件可以为一下方式出现或消失

- 条件渲染 (使用 `v-if`)
- 条件展示 (使用 `v-show`)
- 动态组件
- 组件根节点

```html
<div id="demo">
  <button v-on:click="show = !show">
    Toggle
  </button>
  <transition name="fade">
    <p v-if="show">hello</p>
  </transition>
</div>
<Style>
.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
</Style>
```

初次之外，我们还可以设置状态变化的过度

```

```



##### 自定义指令

##### 过滤器

##### 插件



### Vue3.0

##### 特性

`vue3.0`重要的特点就是讲之前的`option api`改为了`composition api`

也就是`组合式api`，一个功能就是一个`api`

在2.0中，我们创建`vue`对象通常是

```javascript
var vm = new Vue({
  el: '#app',
  data: data
})
```

在3.0中，我们可以改变了方式

```javascript
Vue.createApp({
    data:()=>({
        tag:true
    })
}).moute('#app')
```

##### 优点

* 没有了this的烦恼
* 更好的而类型推到能力（ts）
* 更友好的`Tree-shaking`支持
* 更大的代码压缩空间
* 更灵活的逻辑复用能力











