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

##### 转跳



##### 参数传递



##### keep-alive



### vuex

##### action

##### state

##### getter

##### mutation



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

### 原理

​			







`vue`模块在不同模块规范下有相对应不同的模块可下载，且每种规范下，有分有完整版与运行时版。

- **完整版**：同时包含编译器和运行时的版本。
- **编译器**：用来将模板字符串编译成为 JavaScript 渲染函数的代码。
- **运行时**：用来创建 Vue 实例、渲染并处理虚拟 DOM 等的代码。基本上就是除去编译器的其它一切。

```javascript
// 需要编译器
new Vue({
  template: '<div>{{ hi }}</div>'
})

// 不需要编译器
new Vue({
  render (h) {
    return h('div', this.hi)
  }
})
```

####  实例创建

```vue
/*逻辑层*/
<script>
var data = { message: 'Hello'}
Object.freeze(data)//使得属性不可被修改，响应系统无法再追踪变化
var vm = new Vue({
  	el: '#example',
  	data: data,
    created: function () {
    	// `this` 指向 vm 实例
    	console.log('message is: ' + this.message)
    },
    computed: {
    	// 计算属性的 getter
    	reversedMessage: function () {
      		// `this` 指向 vm 实例
      		return this.message.split('').reverse().join('')
    	}
        //可以自行定义set和get方法
        //set :function(){}
        //get :function(){}
  	},
    methods: {
  		reversedMsg: function () {
    		return this.message.split('').reverse().join('')
  		}
	},
    watch: {
    	reversedMessage: function () {
      		this.msg = this.message.split('').reverse().join('')
    	}
  	}
})
vm.a == data.a //true

</script>

/*显示数据*/
<p>{{ message }}</p>
<p>{{ reversedMessage }}</p>
<p>{{ reversedMsg() }}</p>
<p>{{ msg }}</p>
```

虽然没有完全遵循 [MVVM 模型](https://zh.wikipedia.org/wiki/MVVM)，但是 Vue 的设计也受到了它的启发。因此在文档中经常会使用 `vm` (ViewModel 的缩写) 这个变量名表示 Vue 实例。 

在创建这个实例时，传入参数，通过控制参数来控制渲染一个组件。

