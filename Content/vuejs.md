## Vue.js学习笔记

### 对比

| 对比项 | React | Vue |  
|--|--|--|
| 使用VirtualDOM | 是 | 是 |
| 响应式视图组件 | 是 | 是 |
| 组件化视图组件 | 是 | 是 |
| 丰富的生态系统 | 是 | 否 |
| 运行时性能非常好 | 是 | 否 |
| 自动判断需要渲染的子组件 | 否 | 是 |
| HTML & CSS | 一切都是js，jsx是js+xml的合集，遇到'<'处理为html，遇到'{'处理为xml | 拥抱经典web技术 |


<br/>

| 对比项 | AngularJS | Vue | 
|--|--|--| 
| 语法相似程度 | Vue的灵感来自AngularJS | Vue的灵感来自AngularJS |
| 复杂度 | 复杂 | 简单，解决了AngularJS中存在的许多问题 |
| 数据绑定 | 双向绑定 | 强制单向绑定，是的数据量更清晰 |
| 体积 | 使用AOT+tree-shaking之后130k | vue + vueRouter才30k | 


### 响应式

```
<!-- 控制台输入app.message = "Hello world!"， 则div重新渲染 -->
<script src="https://cdn.jsdelivr.net/npm/vue"></script>

<div id="app">{{ message }}</div>

<script>

var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
})

</script>

```


```
<!-- 
	1. 在span标签上鼠标悬停是会显示span的title
	2. v-bind  是绑定指令，指令一般带有前缀 v-
	3. 控制台输入app.message = "新消息", 则span的title随之变化 	
-->
<script src="https://cdn.jsdelivr.net/npm/vue"></script>

<div id="app-2">
	<span v-bind:title="message">鼠标悬停查看span的title</span>
</div>

<script>

var app = new Vue({
  el: '#app-2',
  data: {
    message: '页面加载于 ' + new Date().toLocaleString()
  }
})

</script>

```


### 指令

1. 条件判断  `v-if`，`v-else`，`v-else-if` 
2. 循环，可以遍历数组和对象属性   `v-for`
3. 事件绑定  `v-on`
4. 表单输入 `v-model`
5. 执行一次的指令   `v-once`
6. 插入html指令，慎用   `v-html`
7. 频繁切换标签是否显示  `v-show`


### 创建组件 `Vue.component`

```
<!-- 
	本例子创建了一个navigation bar，在该navigation bar上展示两个按钮，一个是返回，一个是确定
	
	Vue.component用于注册一个组件：
	  1.app-nav相当于html的标签
	  2.props中用于定义app-nav标签的特性，就相当div的背景颜色这种
	  3.template就是该组件的具体实现
	  4.该例子中，用v-bind绑定了app-nav的navigationbaritems特性和数据navigationBarItemTitles
-->


<script src="https://cdn.jsdelivr.net/npm/vue"></script>


<div id="app">
<app-nav v-bind:navigationbaritems="navigationBarItemTitles"></app-nav>
</div>


<script>

Vue.component('app-nav', {
	props: ['navigationbaritems'],
	template: '<div>  {{ navigationbaritems.leftTitle }} {{ navigationbaritems.rightTitle }} </div>'
})


var app = new Vue({
  el: '#app',
  data: {
  	navigationBarItemTitles: { leftTitle: "返回", rightTitle: "确定" }
  }
})

</script>



```


### 生命周期方法

1. `created`  Vue实例被创建之后执行

2. `counted`

3. `updated` 

4. `destroyed`



### `Mustache`模板语法

1. 计算属性

2. 方法

3. 侦听属性

``` 
<!-- 显示属性msg的值 -->
<span>Message is: {{ msg }}</span>

<!-- 只绑定一次，后面修改msg不会触发span中的显示变化 -->
<span v-once>这个将不会改变: {{ msg }}</span>

<!-- 加载javascript表达式 -->
{{ number + 1 }}
{{ ok ? 'YES' : 'NO' }}
{{ message.split('').reverse().join('') }}

<!-- 每个绑定都只能包含单个表达式，所以下面的例子都不会生效 -->
<!-- 这是语句，不是表达式 -->
{{ var a = 1 }}

<!-- 流控制也不会生效，请使用三元表达式 -->
{{ if (ok) { return message } }}

<!-- 对于复杂的逻辑，应该使用计算属性，比如下面的例子中的computed就是计算属性 -->

<p>Computed reversed message: "{{ reversedMessage }}"</p>

var vm = new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  computed: {
    // 计算属性的 getter
    reversedMessage: function () {
      // `this` 指向 vm 实例
      return this.message.split('').reverse().join('')
    }
  }
})

<!-- 对于复杂的逻辑，methods中也可以定义方法供{{}}调用
	1.methods中不做缓存，每次都调用都会重新计算
	2.computed中，只有当message发生变化的时候才会自动调用
 -->
<p>Computed reversed message: "{{ reversedMessage }}"</p>

var vm = new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  methods: {
    // 计算属性的 getter
    reversedMessage: function () {
      // `this` 指向 vm 实例
      return this.message.split('').reverse().join('')
    }
  }
})

<!-- 侦听属性，fullName会随着firstName和lastName的变化而变化 -->
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar',
    fullName: 'Foo Bar'
  },
  watch: {
    firstName: function (val) {
      this.fullName = val + ' ' + this.lastName
    },
    lastName: function (val) {
      this.fullName = this.firstName + ' ' + val
    }
  }
})

<!-- 计算属性默认是getter，当然也有setter
	getter： fullName会随着firstName和lastName的变化而变化
	setter： fullName发生变化，firstName和lastName也会变化
 -->
var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar'
  },
  computed: {
    fullName: function () {
      return this.firstName + ' ' + this.lastName
    }
  }
})

var vm = new Vue({
  el: '#demo',
  data: {
    firstName: 'Foo',
    lastName: 'Bar'
  },
  computed: {
  	fullName: {
  		get: function(){
      		return this.firstName + ' ' + this.lastName
	  	},
	  	set: function(newValue){
	  		var names = newValue.split(' ')
      		this.firstName = names[0]
      		this.lastName = names[names.length - 1]
	  	}
  	}
  }
})

```


### 绑定class属性

1. 对象语法

2. 数组语法

```
<!-- 对象语法 -->
<!-- 绑定class属性 -->
<div class="static" 
	v-bind:class="{ active: isActive, 'text-danger': hasError }">
</div>

<!-- 数据为 -->
data: {
  isActive: true,
  hasError: false
}

<!-- 结果为 -->
<div class="static active"></div>

```


```
<!-- 数组语法 -->
<!-- 绑定class属性 -->
<div v-bind:class="[activeClass, errorClass]"></div>


<!-- 数据为 -->
data: {
  activeClass: 'active',
  errorClass: 'text-danger'
}

<!-- 结果为 -->
<div class="active text-danger"></div>

```

### 绑定style属性


### 数组

#### 变异方法，会影响原有数组的值

`push()`
`pop()`
`shift()`
`unshift()`
`splice()`
`sort()`
`reverse()`

#### 非编译方法，会返回一个新的数组

`filter()`
`concat()`
`slice()`

#### 注意事项，Vue不能检测以下变动的数组：

1. 当你利用索引直接设置一个项时，例如：vm.items[indexOfItem] = newValue
2. 当你修改数组的长度时，例如：vm.items.length = newLength


