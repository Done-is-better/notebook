#### 全局API

> Vue.directive 用来注册自定义指令，为DOM元素添加新的特性

```vue
//获取焦点
<div id="app">
	<input type="text" v-focus="true"/>
</div>
<script type="text/javascript">
	Vue.directive("focus",{
		inserted(el,binding){		//插入DOM使自动执行inserted()
			if(binding.value){
				el.focus();
			}
		}
	});
	var vm = new Vue({
		el:"#app"
	});
</script>
```

> **Vue.use** 主要用于在Vue中安装插件，可以为Vue添加全局功能。
>
> 插件可以是一个对象或函数，如果是对象必须提供install()方法，用来安装插件；如果是函数，该函数会被当成install()方法。

```vue
//自定义插件并安装
<div id="app" v-my-directive>
</div>
<script type="text/javascript">
	let MyPlugin = {
		install : function(Vue,options){
			Vue.directive("my-directive",{
				bind(el,binding){
					el.style ="width:100px;height:100px;background-color:#ccc";
				}
			});
		}
	}
	Vue.use(MyPlugin,{
		option:true
	});
	var vm = new Vue({
		el:"#app"
	});
</script>
```

> **Vue.extend** 用于基于Vue构造器创建一个Vue子类，可以对Vue构造器进行扩展

```vue
<div id="app1">
	{{title}}
</div>
<div id="app2">
	{{title}}
</div>
		
<script type="text/javascript">
	var Vue2 = Vue.extend({
		data(){
			return{
				title:"new vue"
			}
		}
	});
	var vm1 = new Vue({
		el:"#app1"
	});
	var vm2 = new Vue2({
		el:"#app2"
	});
</script>
```

> **Vue.set**
>
> Vue的核心具有一套响应式系统，简单说就是通过监听器监听数据层的数据变化，当数据改变后，通知视图也自动更新。
>
> Vue.set用于向响应式对象中添加一个属性，并确保这个新属性同样是响应式的，会触发视图更新。

```vue
<div id="app">
	<div>{{a}}</div>
	<div>{{obj.b}}</div>
</div>
<script>
	var vm = new Vue({
		el:"#app",
		data:{
			a:"根级响应式属性a",
			obj:{}
		}
	});
	Vue.set(vm.obj,"b","响应式属性b");
</script>
//直接通过vm.obj.b="xx"添加的二级数据不是响应式的
```

> **Vue.mixin**
>
> 是一种分发Vue组件中可复用功能的非常灵活的方式。混入对象可以包含任意组件选项。当组件使用混入对象时，所有混入对象的选项将被混入该组件本身的选项。
>
> 当组件和混入对象含有同名选项时，这些选项将以恰当的方式混合。

```vue
<div id="app">
	{{msg}}
</div>
<script type="text/javascript">
	Vue.mixin({
			data(){
				return{
					msg:"混入的数据"
				}
			},
			created(){
				console.log("混入的created");
				var myOption = this.$options.myOption;
				if(myOption){
					console.log(myOption.toUpperCase());	
				}
			}
	});
	var vm = new Vue({
			el:"#app",
			myOption:"hello world",
			created(){
				console.log("vue的created 钩子");
			}
	});
</script>
```

#### 实例属性

> **$props**
>
> 使用vm.$props属性可以接受上级组件向下级传递的数据。

```vue
//未实现
<div id="app">
	<my-parent></my-parent>
</div>
<template id="parent">
	<div>
		<h3>手机信息搜索</h3>
		手机品牌：<input type="text" v-model="brand"/>
		<my-child v-bind:name="brand"></my-child>
	</div>
</template>
<template id="child">
	<ul>
		<li>手机品牌：{{show.brand}}</li>
		<li>手机型号：{{show.type}}</li>
		<li>市场价格：{{show.price}}</li>
	</ul>
</template>
<script type="text/javascript">
	Vue.component("my-parent",{
		template:"#parent",
		data(){
			return{
				brand:" "
			}
		}
	});
	Vue.component("my-child",{
		template:"#child",
		props:["name"],
		data(){
			return{
				content:[{
					brand:"华为",
					type:"Meta20",
					price:3699
				},{
					brand:"苹果",
					type:"iPhone7",
					price:2900
				},{
					brand:"三星",
					type:"Galaxy S8+",
					price:3299
				},{
					brand:"Vivo",
					type:"Z5x",
					price:1698
				},{
					brand:"一加",
					type:"OnePlus7",
					price:2999
				},{
					brand:"360",
					type:"N7 Pro",
					price:3600
				}],
				show:{brand:"",type:"",price:""}		//
						
				}
			},
			watch:{
				name(){
					if(this.$props.name){
						var found = false;
						this.content.forEach(
							(value,index) => {
								if(value.brand == this.$props.name){
									found = value;
								}
							}
						); 
						this.show = found? found:{brand:"",type:"",price:""};
					}
				}
			}
		});
		var vm = new Vue({
				el:"#app"
		});
</script>
```

> **$options**
>
> Vue实例初始化时，除了传入指定的选项外，还可以传入自定义选项。自定义选项的值可以使数组、对象、函数等，通知vm.$options来获取。

```vue
//用于获取Vue实例化时传入的选项的值
<div id="app">
	<p>{{base}}</p>
	<p>{{noBase}}</p>
</div>
<script type="text/javascript">
	var vm = new Vue({
		el:"#app",
		customOption:"自定义数据",
		data:{
			base:"data里的基础数据"	
		},
		created(){
			this.noBase = this.$options.customOption;
		}
	});
</script>
```

> **$el** 用于访问vm实例使用的根DOM元素

```vue
<div id="app">
	<p>根元素结构</p>
</div>
<script type="text/javascript">
	var vm = new Vue({
		el:"#app"
	});
	vm.$el.innerHTML = "<div>替换后的div标签</div>"
</script>
```

> **$children**
>
> 用来获取当前实例的直接子组件

```vue
<div id="app">
	<button @click="child">查看子组件</button>
	<my-component></my-component>
</div>
<script>
	Vue.component("my-component",{
		template:"<div>my-component</div>"
	});
	var vm = new Vue({
			el:"#app",
			methods:{
				child(){
					console.log(this.$children);
				}
			}
	});
</script>
```

> **$root**
>
> 用来获取当前组件树的根Vue实例，如果当前实例没有父实例，则获取到的是该实例本身

```vue
<div id="app">
	<my-component></my-component>
</div>
<template id="parent">
	<button @click="root">查看根实例</button>
</template>
<script type="text/javascript">
	Vue.component("my-component",{
		template:"#parent",
		methods:{
			root(){
				console.log(this.$root);
				console.log(this.$root == vm.$root);
			}
		}
	});
	var vm = new Vue({
		el:"#app",	
		data:{}
	});
	console.log(vm.$root);
</script>

//组件实例对象和Vue实例对象都有$root属性
```

> **$slots**
>
> 用于获取插槽。插槽是 父组件向子组件传递内容的

```vue
<div id="app">
	<my-component>hello</my-component>	//my-component组件原本无法显示内容
</div>
<template id="first">
	<div>
		<slot></slot>	//插槽，用于接收自定义组件的内容
	</div>
</template>
<script type="text/javascript">
	Vue.component("my-component",{
		template:"#first"
	});
	var vm = new Vue({
		el:"#app"
	});
</script>

//获取插槽的内容: vm.$children[0].$slots.default[0].text
```

> **$attrs**
>
> 可以获取组件的属性，但其获取的属性中不包含class、style以及被声明为props的属性

```vue
<div id="app">
	<my-component id="test" name="name"></my-component>
</div>
<template id="first">
	<button @click="showAttributes">查看属性</button>
</template>
<script type="text/javascript">
	Vue.component("my-component",{
			template:"#first",
			methods:{
				showAttributes(){
					console.log(this.$attrs);
				}
			}
	});
	var vm = new Vue({
			el:"#app"
	});
</script>
```



+++

#### 全局配置

> **ProductionTip**
>
> Vue.config.productionTip: 打开或关闭生产提示信息，默认为打开状态

```vue
<!--关闭生产提示信息-->
<script>
	Vue.config.productionTip=false;
</script>
```

> **Vue.config.silent**
>
> silent可以取消Vue日志和警告，默认为false

```vue
<div id="app">
	{{msg}}
</div>
<script>
	Vue.config.silent = true;	<!--必须在Vue实例化之前-->
	var vm = new Vue({
		el:"#app",
		data:{}
	});
</script>
```

> **Vue.config.devtools**
>
> 表示打开或关闭vue-devtools调试工具，默认值为true



+++

#### 组件进阶

> **mixins**
>
> mixins是一种分发Vue组件中可复用功能的方式。
>
> mixins对象可以包含任何组件选项,将定义的mixins对象引入组件中即可使用，mixins中的所有选项将会混入到组件自己的选项中。

```vue
<div id="app">	
</div>
<script type="text/javascript">
	var mixin = {
		data(){
			return {
				message:"mixin的数据"
			}
		},
		methods:{
			hello(){
				console.log("Hello from mixin");
			}
		},
		created(){
			console.log("mixin的钩子函数");
		}
	}
	var vm = new Vue({
			el:"#app",
			mixins:[mixin],
			data:{},
			created(){
				console.log("Vue的钩子函数");
			}
	});
</script>

<!--混入函数与vue选项冲突：
1。如果是数据或函数冲突，Vue优先
2.如果是钩子函数冲突，都执行，先执行混入对象的钩子函数，然后执行Vue的钩子函数-->
```

> **render**
>
> 在Vue中可以使用Vue.render()实现对虚拟DOM的操作。
>
> 在Vue中一般使用templae来创建HTML,但这种方式可编程性不强，而使用Vue.render()可以更好的发挥JS的编程能力

```vue
<div id="app">
	<my-component>成功渲染</my-component>
</div>
<script type="text/javascript">
		Vue.component("my-component",{
			render(createElement){
				return createElement("p",{
					style:{
						color:"red",
						fontSize:"16px",
						backgroundColor:"#eee"
					}
				},this.$slots.default);
			}
		});
		var vm = new Vue({
			el:"#app"
		});
</script>

<!--render是一个函数，它作为一个选项存在，可以再Vue中和组件中使用这个选项；
	render的参数：createElement是一个用于创建元素的函数。-->
```

> **createElement**
>
> createElement()函数返回的并不是一个实际的DOM元素，而是一个描述节点(createNodeDescription)
>
> 第一个参数可以是一个HTML标签名或组件选项对象
>
> 第二个参数是可选的，可以传入一个与模板中的属性对应的数据对象。
>
> 第三个参数是由createElement()构建而成的子级虚拟节点，也可以使用字符串来生成文本虚拟节点。

```vue
<div id="app">
	<my-component>
		<template v-slot:header>
			<div style="background-color:#ccc;height:50px">导航栏</div>
		</template>
		<template v-slot:content>
			<div style="background-color:#aaa;height:50px">内容区</div>
		</template>
		<template v-slot:footer>
			<div style="background-color:#eee;height:50px">底栏</div>
		</template>
	</my-component>
</div>
<script type="text/javascript">
	Vue.component("my-component",{
			render(createElement){
				return createElement("div",[
						createElement("header",this.$slots.header),
						createElement("content",this.$slots.content),
						createElement("footer",this.$slots.footer)
				])
			}
	});
	var vm = new Vue({
		el:"#app"
	});
</script>
```

