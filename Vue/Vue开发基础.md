> **Vue实例配置对象**
>
> data	Vue实例数据对象
>
> methods	定义Vue实例中的方法
>
> components	定义子组件
>
> computed	计算属性
>
> filters	过滤器
>
> el	唯一的根元素
>
> watch	监听数据变化

*el唯一根标签：* 在创建Vue实例时，通过指定class或id选择器可用来将页面结构与Vue实例对象进行绑定

*data初始数据:* Vue实例的数据对象为data, Vue会将data的属性转换为getter 和setter，从而让data的属性能够响应数据变化

​	可以使用	vm.$data.msg 访问data对象的msg数据，其中$data可以省略

```javas
//两种写法
methods:{
fun1(){}
fun2:function(){}
}
```

```javas
<div id="app">
	<button @click="inf">单击</button>
	<p>{{msg}}</p>
</div>
<script type="text/javascript">
	var vm = new Vue({
		el:"#app",
		data:{
			msg:""
		},
        methods:{
			inf(){
				this.msg = "点击后的Info"
			},
			info: function (){
				this.msg = "inf方法"
			}
		}
	});
</script> 
```



*computed计算属性：* 计算属性结果会被缓存起来，当依赖的响应式属性发生变化时，才会重新计算，返回最终结果。

```javas
<div id="app">
	<p>总价格： {{totalPrice}}</p>
	<p>单价： {{price}}</p>
	<p>数量：	{{num}}</p>
	<button @click="num==0?0:num--">减少一个</button>
	<button @click="num++">增加一个</button>
</div>
<script type="text/javascript">
	var vm = new Vue({
		el:"#app",
		data:{
			num :0,
			price:20
		},
		computed:{
			totalPrice(){
				return this.price*this.num;
			}
		}
	});
</script>
```



*watch状态监听：* 用来监测Vue实例中的数据变动

```javas
<div id="app">
	<input type="text" v-model="cityName"/>
</div>
<script type="text/javascript">
	var vm = new Vue({
		el:"#app",
		data:{
			cityName:"shanghai"
		},
		watch:{
			cityName(newValue,oldValue){	//函数名和要监听的字段名保持一致，第一个形参表示改变后的值，
											//第二个形参表示改变前的值
					console.log(newValue,oldValue);
			}
		}
	});
</script>
```



*filters过滤器：* 对数据进行格式化。

```javascript
//插值表达式使用filters
<div id="app">
	<p>{{message | toUppercase}}</p>
</div>
<script type="text/javascript">
	var vm = new Vue({
		el:"#app",
		data:{
			message:"Hello world !"
		},
		filters:{
			toUppercase(value){
				return value ? value.toUpperCase():'';
			}
		}
	});
</script>
```

```javas
//属性绑定v-bind使用过滤器
<div id="app">
	<div v-bind:id="dataId | formatId">Hello World</div>
</div>
<script type="text/javascript">
	var vm = new Vue({
		el:"#app",
		data:{
			dataId:"lucheng"
		},
		filters:{
			formatId(value){
				return value? value.charAt(1) + value.indexOf('u'):'';
			}
		}
	});
</script>
```



+++

+++

#### v-bind

```javas
//绑定样式
<div id="app">
	<div v-bind:style="{backgroundColor:pink,width:width,height:height}">
		<div v-bind:style="myDiv"></div>	//绑定多个样式v-bind:style="[myDiv1,myDiv2]"
	</div>
</div>
<script type="text/javascript">
	var vm = new Vue({
		el:"#app",
		data:{
			pink:"pink",
			width:"100%",
			height:"300px",
			myDiv:{
				backgroundColor:"red",
				width:"20%",
				height:"100px"
			}
		}
	});
</script>
```

```javascript
//绑定类
<div id="app">
	<div v-bind:class="box">
		<div v-bind:class="inner"></div>
		<div v-bind:class="[inner,inner2]"></div>
	</div>
</div>
<script type="text/javascript">
	var vm = new Vue({
		el:"#app",
		data:{
			box:"box",
			inner:"inner",
			inner2:"inner2"
		}
	});
</script>
```





+++

#### Vue常用的内置指令

> <a>v-model</a>	双向数据绑定
>
> <a>v-on</a> 	监听事件
>
> <a>v-bind</a> 	单向数据绑定
>
> <a>v-text</a> 	插入文本内容
>
> <a>v-html</a> 	插入包含HTML的内容
>
> <a>v-for</a> 	列表渲染
>
> v-if	条件渲染
>
> v-show	显示隐藏

***v-model:***

```javas
<div id="app">
	<input type="text" v-model="msg"/>
</div>
<script type="text/javascript">
	var vm = new Vue({
		el:"#app",
		data:{
			msg:"v-model指令"
		}
	});
//数据改变影响界面，界面改变影响数据
```

***v-text:*** 在DOM元素内部插入文本内容

```javas
<div id="app">
	<p v-text="msg"></p>
</div>
<script type="text/javascript">
	var vm = new Vue({
		el:"#app",
		data:{
			msg:"new message"
		}
	});
</script>

//数据改变影响界面，界面无法改变
```

***v-html*** 在DOM 元素内部插入HTML标签内容

```javas
<div id="app">
	<div v-html="msg"></div>
</div>
<script>
	var vm = new Vue({
		el:"#app",
		data:{
			msg:"<button>message</button>"
		}
	});
</script>
//单向绑定
```

***v-bind*** 可以实现属性单向数据绑定

```javas

<div id="app">
	<input type="text" v-bind:value="msg"/>
</div>
<script type="text/javascript">
	var vm = new Vue({
		el:"#app",
		data:{
			msg:"message"
		}
	});
</script>
//单向绑定
```

***v-on*** 负责给DOM元素绑定事件，配合事件类型使用

***可以简写成 @*** (v-on:click   <==>  @click)

```javasc
<div id="app">
	<p>{{msg}}</p>
	<p v-text="msg"></p>
	<button v-on:click="click">click</button>
</div>
<script type="text/javascript">
	var vm = new Vue({
		el:"#app",
		data:{
			msg:"请单击"
		},
		methods:{
			click(){
				this.msg = "已点击"
			}
		}
	});
</script>
```

***v-for*** 可以用来实现页面列表渲染，常用来循环数组

```javas
<div id="app">
	<div v-for="(value ,index) in list">
		元素是：{{value}},索引是：{{index}}
	</div>
</div>
<script type="text/javascript">
	var vm = new Vue({
		el:"#app",
		data:{
			list:[1,2,3,4,5]
		}
	});
</script>
```

***v-if和 v-show*** 用来控制元素显示或隐藏，属性为布尔值

```javascript
//v-if
<div id="app">
	<div v-if="isShow" style="background-color:#ccc">div</div>
	<button @click="isShow=!isShow">hidden/show</button>
</div>
<script type="text/javascript">
	var vm = new Vue({
		el:"#app",
		data:{
			isShow:true
		}
	});
</script>
```

> v-if 判断页面是否渲染，为false时，页面不会渲染
>
> v-show 控制display属性





+++

+++

#### Vue事件

> 在Vue中可以使用内置指令v-on监听DOM事件，并在触发时运行一些JavaScript代码，或绑定事件处理方法

```javas
<div id="app">
	<button @click="count += Math.ceil(Math.random())">随机数</button>
	<p>自动生成的随机数为： {{count}}</p>
</div>
<script type="text/javascript">
	var vm = new Vue({
		el:"#app",
		data:{
			count:0
		}
	});
</script>
```

#### 按键修饰符

```javas
<div id="app">
	<input type="text" v-on:keyup.enter="submit"/>
</div>
<script type="text/javascript">
	var vm = new Vue({
		el:"#app",
		methods:{
			submit(){
				console.log("提交");
			}
		}
	});
</script>
```

#### 常用事件修饰符

> .stop	阻止事件冒泡
>
> .prevent	阻止默认事件行为
>
> .capture	事件捕捉
>
> .self	将事件绑定到自身，只有自身才能触发
>
> .once	事件只触发一次

***.stop事件修饰符***

> 在前端开发中，复杂的页面结构需要很多事件来完成交互行为
>
> 默认的事件传递方式是冒泡，同一个事件类型会在元素内部和外部触发，有可能会造成事件的错误触发，所以就需要.stop修饰符阻止事件冒泡行为

```javascri
<div id="app">
	<div @click="doParent">
		<button @click="doThis">first</button>
		<button @click.stop="doThis">second</button>
	</div>
</div>
<script type="text/javascript">
	var vm = new Vue({
		el:"#app",
		methods:{
			doParent(){
				console.log("Parent");
			},
			doThis(){
				console.log("This");
			}
		}
	});
</script>
```

***.prevent事件修饰符***

*在实际开发中，如果<a>标签的默认行为与事件发生冲突，可以使用.prevent修饰符来阻止<a>标签的默认行为*

```javasc
<div id="app">
	<a href="http://www.baidu.com" @click.prevent>阻止默认行为</a>
	<a href="http://www.baidu.com">不阻止默认行为</a>
</div>
<script type="text/javascript">
	var vm = new Vue({
		el:"#app",
		data:{
					
		}
	});
</script>
```

***.capture事件修饰符***

事件捕获的执行顺序是由外部结构向内部结构执行，与事件冒泡顺序相反

```javas
<div id="app">
	<div @click.capture="doParent">
		<button @click="doThis">事件捕获</button>
	</div>
</div>
<script type="text/javascript">
	var vm = new Vue({
		el:"#app",
		data:{
					
		},
		methods:{
			doParent(){
				console.log("Parent");
			},
			doThis(){
				console.log("This");
			}
		}
	});
</script>
```

***.capture 要添加到父元素上，否则不生效***

***.self事件修饰符***  用来实现只有DOM 元素本身会触发事件,冒泡不会触发。

```javas
<div id="app">
	<div class="odiv1" @click.self="doParent">a
		<div class="odiv2" @click="doThis">b</div>
	</div>
	<div class="odiv1" @click="doParent">c
		<div class="odiv2" @click="doThis">d</div>
	</div>
</div>
<script type="text/javascript">
	var vm = new Vue({
		el:"#app",
		data:{
					
		},
		methods:{
			doParent(){
				console.log("doParent");
			},
			doThis(){
				console.log("doThis");
			}
		}
	});
</script>
```

***.once事件修饰符***

只触发一次事件处理函数

```javascript
<div id="app">
	<button @click.once="doOnce">once</button>
</div>
<script type="text/javascript">
	var vm = new Vue({
		el:"#app",
		methods:{
			doOnce(){
				console.log("doOnce");
			}
		}
	});
</script>
```





+++

+++

#### Vue组件

> 在Vue中，组件是构成页面中独立结构单元，组件主要以页面结构形式存在，不同组件也具有基本交互功能。

> **组件特性**
>
> - 减少重复代码的编写，提高开发效率。
> - 降低代码之间的耦合程度，使项目更易维护和管理。
> - 根据业务逻辑实现复杂的项目功能。

```javascript
<div id="app">
	<my-component></my-component>
	<my-component></my-component>
	<my-component></my-component>
</div>
<script type="text/javascript">
	Vue.component("my-component",{
		data(){
			return {
				count:0
			}
		},
		template:"<button @click='count++'>被点击了{{count}}次</button>"
	});
	var vm = new Vue({
		el:"#app"
	});
</script>
```

> **局部注册组件**
>
> Vue.component() 方法用于全局注册组件，还可以通过Vue实例的components属性局部注册

```javas
<div id="app">
	<my-component></my-component>		//HTML中不能使用驼峰命名法
</div>
<script type="text/javascript">
	var com1 = {
		template: "<p>xxxxxx</p>"
	}
	var vm = new Vue({
		el:"#app",
		components:{
			myComponent:com1			//作为key单词中不能有短横线，可以写成'my-component'
		}
	});
</script>
```

> **template模板**
>
> Vue提供了 <template>标签来定义结构的模板，可以在该标签中写HTML代码，然后通过id值绑定到组件的 template属性上，这样有利于在编辑器中显示代码提示和高亮显示

```javas
<div id="app">
	<p>{{title}}</p>
	<my-component></my-component>
</div>
<template id="temp1">
	<p>{{title}}</p>
</template>
<script>
	Vue.component("my-component",{
		template:"#temp1",
		data(){
			return {
				title:"组件内的title"
			}
		}
	});
	var vm = new Vue({
		el:"#app",
		data:{
			title:"vue实例对象的title"
		}
	});
</script>
```



> **组件之间的数据传递**
>
> 组件之间数据传递需要借助一些工具来实现父组件向子组件传递数据信息

> **props**
>
> 用来接收父组件中定义的数据，其值为数组，数组中是父组件传递的数据信息

```javas
//父组件向子组件传值
<div id="app">
	<my-parent name="title"></my-parent>		//父组件
</div>
<script type="text/javascript">
	Vue.component("my-parent",{
		props:["name"],
		template:"<div>父组件的{{name}}</div>"		//子组件
	});
	var vm = new Vue({
		el:"#app"
	});
</script>
```

> **$emit传值**
>
> 能将子组件中的值传递到父组件中。$emit可以触发父组件中定义的事件，子组件的数据信息通过传递参数的方式完成。

```javas
<div id="app">
	<parent></parent>
</div>
<template id="child">
	<div>
		<button @click="click">send</button>
		<input type="text" v-model="message"/>
	</div>
</template>
<template id="parent">
	<div>
		<child @childfn="transContent"></child>
		<p>子组件传递的值: {{message}}</p>
	</div>
</template>
<script type="text/javascript">
	Vue.component("parent",{
		template:"#parent",
		data(){
			return{
				message:""
			}
		},
		methods:{
			transContent(value){
				this.message = value;
			}
		}
	});
	Vue.component("child",{
		template:"#child",
		data(){
			return {
				message:"子组件的message"
			}
		},
		methods:{
			click(){
				this.$emit("childfn",this.message);
			}
		}
	});
	var vm = new Vue({
		el:"#app",
		data:{
			message:"message"
		}
	});
</script>
```

+++

> **组件切换**
>
> Vue中的页面结构是由组件构成的，不同组件可以表示不同页面，适合进行单页面应用开发

```javas
<div id="app">
	<a href="#" @click.prevent="flag=true">login</a>
	<a href="#" @click.prevent="flat=false">logup</a>
	<login v-if="flag"></login>
	<register v-else></register>
</div>
<script type="text/javascript">
	Vue.component("login",{
		template:"<div>登录页面</div>"
	});
	Vue.component("register",{
		template:"<div>注册页面</div>"
	});
	var vm = new Vue({
		el:"#app",
		data:{
			flag:true
		}
	});
</script>
```



#### Vue的生命周期

+++

>**钩子函数** 	----用来描述Vue实例从创建到销毁的整个生命周期
>
>beforeCreate	创建实例对象之前执行
>
>created	创建实例对象之后执行
>
>beforeMount	页面挂载成功之前执行
>
>mounted	页面挂载成功之后执行
>
>beforeUpdate	组件更新之前执行
>
>updated	组件更新之后执行
>
>beforeDestroy	实例销毁之前执行
>
>destroyed	实例销毁之后执行



***beforeCreate	created***

```javas
<div id="app">
	{{msg}}
</div>
<script type="text/javascript">
	var vm = new Vue({
		el:"#app",
		data:{
			msg:"message"
		},
		beforeCreate(){
			console.log("vue实例创建之前");
			console.log(this.msg);
		},
		created(){
			console.log("vue 实例创建之后");
			console.log(this.$data.msg);
		}
	});
</script>
```

```javas
//beforeMount  mounted
<div id="app">
	{{msg}}
</div>
<script type="text/javascript">
	var vm = new Vue({
		el:"#app",
		data:{
			msg:"message"
		},
		beforeMount(){
			console.log(this.$el.innerHTML);
		},
		mounted(){
			console.log(this.$el.innerHTML);
		}
	});
</script>
```

```javas
//beforeUpdate updated
<div id="app">
	<div v-if="isShow" ref="div">test</div>
	<button @click="isShow = !isShow">update</button>
</div>
<script type="text/javascript">
	var vm = new Vue({
		el:"#app",
		data:{
			isShow:true
		},
		beforeUpdate(){
			console.log("更新数据之前");
			console.log(this.$refs.div);
		},
		updated(){
			console.log("更新数据之后");
			console.log(this.$refs.div);
		}
	});
</script>
```

```vue
//beforeDestroy destroy
<div id="app">
	<div ref="div">test</div>
</div>
<script type="text/javascript">
	var vm = new Vue({
		el:"#app",
		data:{
			msg:"message"
		},
		beforeDestroy(){
			console.log("销毁之前");
			console.log(this.$refs.div);
			console.log(this.msg);
			console.log(vm);
		},
		destroyed(){
			console.log("销毁之后");
			console.log(this.$refs.div);
			console.log(this.msg);
			console.log(vm);
		}
	});
</script>

销毁之前可以操作DOM对象，销毁之后不能操作DOM
销毁之后vue实例对象依然存在
```

 
