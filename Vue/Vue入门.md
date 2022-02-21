#### VDOM

> 虚拟DOM可以提高浏览器的渲染速度，对比操作JS对象，操作真实的DOM性能消耗较多
>
> **虚拟DOM的实现步骤：**
>
> - 在页面首次渲染时，将要渲染的数据全部加载到虚拟的DOM中，然后一次性渲染到真实的DOM
> - 在数据变化时，额外的生成一棵虚拟DOM树
> - 通过Diff算法对比修改的部分，然后将修改的部分渲染到真实的DOM中
> - 释放内存

#### 初识Vue

> Vue是一套用于构建用户界面的渐进式框架
>
> - 与其他大型框架相比,Vue被设计为可以自底向上逐层应用
> - Vue可以开发一个全新项目，也可以将Vue引入到一个现有的项目中

> **Vue的设计思想: MVVM**
>
> Model  --  ViewModel --  View
>
> 视图到模型：采用监听的方式，视图数据变化会影响到模型数据
>
> 模型到视图：采用绑定的方式，模型数据变化会影响到视图数据
>
> 以上是数据绑定，基于MVVM设计思想

> **Vue的优势**
>
> - 轻量级：Vue简单、直接
> - 数据绑定：数据驱动视图，视图也驱动数据
> - 指令：指令绑定在元素上时，指令会给绑定的元素添加一些特殊的行为
> - 插件：插件用于对Vue框架功能进行扩展

> **npm包管理工具**
>
> npm install :	安装项目所需要的全部包，需要配置package.json文件
>
> npm install 包名:	安装指定名称的包
>
> npm uninstall 包名: 	卸载指定名称的包
>
> npm update 包名：	更新指定名称的包
>
> npm start：	项目启动
>
> npm run build:	项目构建
>
> 配置npm服务器为淘宝服务器： npm config set regitry https://registry.npm.taobao.org

```javasc
		<div id="app">
			<!--插值表达式显示Vue的数据-->
			{{msg}}
		</div>
		<script>
			var vm = new Vue({
				el:"#app", //表示当前Vue控制的页面区域
				data:{
					msg:"Hello Vue.js" //表示当前Vue控制的区域的数据
				}
			});
```

> **webpack安装**
>
> npm install webpack webpack-cli -g	安装webpack
>
> npm uninstall webpack webpack-cli -g 	卸载

经过webpack打包的工具响应更快，更稳定

```javascript
 webpack ./src/hello.js ./src/world.js -o ./build/app.js --mode=development
```



#### 构建Vue项目

```javascript
vue init webpack myweb	//创建一个webpack模板的目录结构
```



#### Vue项目目录结构

> build	项目构建webpack相关代码
>
> config	配置文件目录
>
> node_modules	依赖模块
>
> src	源码目录
>
> static	静态资源目录
>
> test	初始测试目录
>
> index.html	首页入口文件
>
> package.json	项目配置文件
>
> readme.md	项目说明文件

```javascript
//启动项目
cd myapp
npm run dev
```

