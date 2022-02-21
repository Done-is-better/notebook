#### 初识路由

> **后端路由**
>
> 路由的本质就是一种对应关系，<a>url</a>地址和真实的资源之间就有一种对应关系，就是路由。
>
> 后端路由是由服务器端进行实现，并完成资源的分发

> **前端路由**
>
> 就是把不同路由对应不同的内容或页面的任务交给前端来做。对于单页面SPA来说，主要通过URL中的hash来实现不同页面之间的切换。hash有一个特点，就是HTTP请求中不会包含hash相关的内容，所以单页面程序中的跳转主要用hash来实现。
>
> 前端路由是依靠hash值的变化进行实现。	



+++

#### vue-router

> **<a>vue-router</a>工作原理**
>
> 它是一个<a>Vue.js</a>官方提供的路由管理器，是一个功能强大的前端路由器。它是单页面应用(SPA)的核心思想之一，就是更新视图而不重新请求页面，简单地说，它在加载页面时，不会加载整个页面，只会更新某个指定的容器中的内容。
>
> 在实现单页面前端路由时，提供了两种方式，分别是hash模式和history模式，根据mode 参数来决定采用哪一种方式。

> **hash模式**
>
> <a>vue-router</a>默认为hash模式，使用<a>url</a>的hash来模拟一个完整的URL，当URL改变时，页面不会重新加载。 #就是hash符号，中文名为哈希符或锚点，在hash符号后的值，成为hash值。

> **vue-router工作原理**
>
> 路由的hash模式是利用了window 可以监听onhashchange事件来实现的，也就是说hash值是用来指导浏览器动作的，对服务器没有影响，HTTP请求中也不会包括hash值，同时每一次改变hash值，都会在浏览器的访问历史中增加一个记录，使用“后退"按钮，就可以回到上一个位置。所以hash模式是根据hash值来发生改变，根据不同的值，渲染指定DOM 位置的不同数据。

> **history模式**
>
> history模式不会出现#,这种模式充分利用history.pushState()来完成URL的跳转而且无需重新加载页面。使用history模式时，需要在路由规则配置中增加mode:'history'.

> route:	表示它是一条路由，单数形式
>
> routes:	表示是一组路由，把route的每一条路由组合起来，形成一个数组
>
> router:	表示它是一个机制，充当管理路由的管理者角色。

```vue
 <div id="app">
        <router-link to="/login" tag="span">login</router-link>
        <router-view></router-view>
     
     <!--router-link是路由链接，to代表链接地址，tag代表渲染的标签
			router-view是路由视图，用于显示路由链接对应的组件
		-->
    </div>
    <script>
        var login = {
            template: "<h1>登录组件</h1>"
        }
        var router = new VueRouter({
            routes: [{
                path: "/login",
                component: login
            }]
        });
        
        <!--
            在实例化VueRouter的时候传入routes,通过routes定义路由 匹配规则，可以定义多个
            匹配规则是一个对象，key是路由链接path,value是组件component
            -->
        var vm = new Vue({
            el: "#app",
            router
        });
    </script>
```

> **路由对象属性**
>
> 路由对象表示当前激活的路由状态信息，包含了当前URL解析得到的信息，还有URL匹配到的路由记录。路由对象是不可变的，每次成功导航后都会产生一个新的对象。
>
>  
>
> this.$router表示全局路由器对象，项目中通过router路由参数注入路由之后，在任何一个页面都可以通过该属性获取到路由器对象，并调用其push()、go()等方法。this.$route表示当前正在跳转的路由器对象，可以访问其name,path,query,params等属性。



> **路由对象$route的常用属性**
>
> <a>$route.path</a>  String  对应当前路由的路径
>
> <a>$route.query</a>  Object  一个{key:value}对象，表示URL查询参数
>
> <a>$route.params</a> Object 一个{key:value}对象，路由跳转携带参数
>
> <a>$route.hash</a>  String 在history模式下获取当前路由的hash值，如果没有hash值，则为空字符串
>
> <a>$route.fullPath</a> String 完成解析后的URL，包含查询参数和hash的完整路径
>
> <a>$route.name</a> String 当前路由的名称
>
> <a>$route.matched</a> Array 路由记录，当前路由下路由声明的所有所有信息，从父路由到当前路由为止
>
> <a>$route.redirectedFrom</a> String  如果存在重定向，即为重定向来源的路由



+++

#### 用户登录注册案例

1. 初始化项目：***npm init -y *** (-y表示全部默认)  会在项目下生成一个<a>package.json</a> 工程文件，记录依赖需要的包

2. 安装vue和vue-router:***npm install vue@2.2.x vue-router@XXX***

3. 安装webpack: *** npm install webpack@4.39.x webpack-cli@xxx webpack-dev-server@xxx html-webpack-plugin@xxx ***   项目中需要打包工具，所以需要用到webpack打包工具，实现自动打包编译功能。为了更方便的使用webpack，还需要安装webpack-cli工具、webpack-dev-server服务器和html-webpack-plugin插件

4. 修改package.json文件，在scripts中添加dev，使用webpack-dev-server来启动项目：*** “scritp":{"dev":"webpack-dev-server --inline --hot --port 8080"}  ***  之后运行项目时可以使用 npm run dev

5. 编写webpack.comfig.js文件。配置webpack的选项，设置入口文件、出口文件及一些规则配置。

6. 安装vue-loader和vue-template-compiler    vue-loader的作用是解析和转化vue文件，提取其中的script、style、HTML、template,然后分别把它们交给各自相对的loader去处理。 vue-template-compiler的作用是把vue-loader提取出的HTML模板编译成对应的可执行的javascript代码

7. 在module.exports中找到module,在rules数组中配置loader加载依赖：***modules:{rules:[{test:/\.vue$/,use:"vue-loader"},]}**

8. 安装css-loader和style-loader  ***npm install css-loader@xxx style-loader@xxx*** css-loader和style-loader用来处理样式文件。css-loader用于加载由vue-loader提供取出的css文件，再用style-loader添加到页面

9. 在webpack.config.js中添加rules规则***test:/\.css$/, use:["style-loader","css-loader"]**

   1. 安装css预处理器  通过css预处理器可以使用专门的编程语言来编写页面样式，然后编译成正常的css文件，供项目使用。css预处理器为css增加了一些编程的特性，用户无需考虑浏览器的兼容性问题，可以使css更加简洁、更具有适用性和可读性，更易维护。常用的css预处理器有less、Sass/SCSS 
   2. 安装MUI

   
   1. 图片和字体处理.由于项目中使用了外部mui样式库，其中包含后缀名为ttf的文件，webpack无法处理该类文件，所以需要安装对应的loader来处理    file-loader和url-loader都可以在package中处理图片、字体图标等文件，后者可以将图片转为base64字符串，能更快地加载图片，并且可以通过limit属性对图片进行分情况处理，当图片小于limit大小时转为base64，大于limit时调用file-loader对图片进行处理

