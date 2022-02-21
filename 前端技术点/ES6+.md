 ### ES6

##### let

> 1. 变量不能重复声明
> 2. 存在块作用域
> 3. 不存在变量提升
> 4. 不影响作用域链

##### const

> 1. 必须赋初始值
> 2. 不能修改
> 3. 存在块级作用域
> 4. 如果常量是个数组，对元素修改不改变地址

##### 解构赋值

```javascript
const f4 = ['xxx','xxxx']
let [a,b] = f4
const obj = {
    name:'xxx',
    say(){
        ...
    }
}
let {name,say} = obj
```

##### 箭头函数

> 1. this是静态的，始终指向函数声明时所在作用域下的this 的值
> 2. 不能作为构造实例化对象
> 3. 不能使用 arguments 变量
> 4. 代码体只有一条语句时可以省略大括号，此时必须省略return

![image-20220125210859879](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20220125210859879.png)

> 箭头函数适用于与 this 无关的回调，如定时器，数组方法的回调
>
> 不适用于与 this 有关的回调，如事件回调，对象方法

##### 函数参数的初始值

> 允许给函数设置初始值，调用时没传实参时使用(习惯上放到最后一个参数)

```javascript
// 形参初始值 配合 解构赋值使用
function fun({user='joey',passwd:'123'}){
    console.log(user,passwd)
}
fun({user:'root'})
===> root,123
```

##### rest

> 引入rest 参数，用来代替ES5 arguments

```javascript
//ES5
function fun(){
    console.log(arguments)
}
fun('1','2','3','4')
//rest 优点 数组
function fun(...args){
    console.log(args)
}
fun('joey','tom','emily')
```

> rest 参数必须放在参数的最后

##### 扩展运算符

> 将数组转为参数序列

```javascript
//数组合并
const arr1 = ['a','b']
const arr2 = ['c','d']
const arr3 = [...arr1,...arr2] //['a','b','c','d']
```

##### Symbol

> ES6 引入了一种新的原始数据类型，表示独一无二的值
>
> 1. Symbol的值是唯一的，用来解决命名冲突的问题
> 2. Symbol的值不能与其他数据进行运算
> 3. Symbol定义的对象属性不能使用for...in 循环遍历，但可以使用Reflect.ownKeys来获取对象的所有键名

![image-20220125215833788](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20220125215833788.png)

s4 === s5 true

```javascript
//避免命名冲突
let game = {
  up(){console.log('hello')},
  down(){console.log('world')}
}
let method = {
  up:Symbol(),
  down:Symbol()
}
game[method.up] = () =>{console.log('up')}
game[method.down] = () =>{console.log('down')}
```

**<a>ES6 提供了11个内置Symbol值</a>**

|                                  |                                                              |
| -------------------------------- | ------------------------------------------------------------ |
| <a>Symbol.hasInstance</a>        | 其他对象使用instanceof时，调用此方法                         |
| <a>Symbol.isConcatSpreadable</a> | 对象的Symbol.isConcatSpreadable属性等于的是一个布尔值，表示该对象用于Array.prototype.concat()时，是否可以展开 |
| <a>Symbol.upscopables</a>        | 指定了使用with 关键字时，那些属性会被with环境法排除          |
| <a>Symbol.match</a>              | 当执行 str.match(obj)时，如果该属性存在，会调用他，返回方法的返回值 |
| <a>Symbol.replace</a>            | 当该对象被str.replace(obj)调用时，返回该方法的返回值         |
| <a>Symbol.search</a>             | 当该对象被str.search(obj)方法调用，会返回该方法的返回值      |
| <a>Symbol.split</a>              | 当该对象被str.split(obj)方法调用时，返回该方法的返回值       |
| <a>Symbol.iterator</a>           | 对象进行for...of循环时，调用，返回该对象的默认遍历器         |
| <a>Symbol.toPrimitive</a>        | 该对象被转为原始类型的值时，回调用这个方法，返回该对象对应的原始类型值 |
| <a>Symbol.toStringTag</a>        | 在该对象上调用toString方法，返回该方法的返回值               |
| <a>Symbol.species</a>            | 创建衍生对象时，使用该属性                                   |

```javascript
//Symbol.hasInstance
class Ver{
  static [Symbol.hasInstance](param){
    console.log('res === ')
    if(param === 'ver') return true
  }
}
console.log('var' instanceof Ver)
console.log('ver' instanceof Ver)
//Symbol.isConcatSpreadable
let arr1 = [1,2,3]
let arr2 = [4,5,6]
arr2[Symbol.isConcatSpreadable] = false //不展开，使用arr2作为一个对象和arr1合并
console.log(arr1.concat(arr2))
```

##### 迭代器

> Iterator 是一种接口，为各种不同的数据结构提供统一的数据访问机制
>
> 1. ES6 提供了一种新遍历方式 for...of...，Iterator 接口主要共for...of消费
>
> 2. 原生的具备iterator接口的数据
>
>    <a>Array,Arguments,Set,String,TypedArray,NodeList</a>
>
> 3. 工作原理
>
>    1. 创建一个指针对象，指向当前数据结构的起始位置
>    2. 第一次调用对象的next方法，指针自动指向数据结构的第一个成员
>    3. 调用next方法，指针后移

```javascript
let arr3 = ['joey','zoe','emily','tom']
let iterator = arr3[Symbol.iterator]() //获取arr3的iterator
console.log(iterator.next())
console.log(iterator.next())
```

##### 生成器

//用来解决回调地狱

```javascript
function * gen(){
  console.log('hello')
  yield '123'
  console.log('world')
  yield '234'
  console.log('3')
  yield '345'
  console.log('4')
}

let iter = gen()
iter.next()
iter.next()
iter.next()
iter.next()
```

```javascript
function * gen(args){
  console.log(args)
  let a = yield '123'
  console.log(a)
  const b = yield '234'
  console.log(b)
  const c = yield '345'
  console.log(c)
}

let iter = gen('AAA')
iter.next('a')
iter.next('b')
iter.next('c')
iter.next()
```



```javascript
//回调地狱的另一种解决方法
function one(){
  setTimeout(() => {console.log('111');iter.next()},1000)
}
function two(){
  setTimeout(() => {console.log('222');iter.next()},1000)
}
function three(){
  setTimeout(() => {console.log('333');iter.next()},1000)
}
function * generator(){
  yield one()
  yield two()
  yield three()
}
let iter = generator()
iter.next()
```

##### Promise

> 1. Promise 构造函数
> 2. Promise.prototype.then方法
> 3. Promise.prototype.catch

```javascript
let ajax = new Promise((resolve,reject) =>{
  setTimeout(() =>{
    let msg = 'success'
    let err = 'fail'
    // resolve(msg)
    reject(err)
  },1000)
})
ajax.then(value =>{
  console.log(value)
},err =>{
  console.log(err)
})
```

##### set

```javascript
//数组去重
let arr = [1,1,2,3,5]
let res = [...new Set(arr)] //Set是个对象
```

```javascript
//交集
let arr = [1,2,3,4,5]
let ar = [3,4,5,6,7]
let resu = [...new Set(arr)].filter(item => new Set(ar).has(item))
console.log(resu)
```

##### map

```javascript
let map = new Map()
map.set('key','value')
map.set('k1',{name:'joey'})
map.set('k2',['1',2])
console.log('map -- ',map)
console.log('getmap',map.get('k1'))
console.log(map.keys())
console.log('entries',map.entries())
```

##### 新增API

```javascript
console.log(Number.EPSILON) //js 中最小精度2.220446049250313e-16
console.log(Number.isFinite(Math.PI)) //判读是否为有限数
console.log(Number.isNaN(Math.E))
console.log(Number.parseInt('3.14'))
console.log(Number.parseFloat('3.14'))
console.log(Number.isInteger(Math.E))
console.log(Math.trunc(Math.E)) //去除小数部分
console.log(Math.sign(234)) //判断数字是否为正、负数、零(1,0,-1)
```

```javascript
console.log(Object.is(NaN,NaN)) //true 判断两个数值是否相等，与 === 有区别
console.log(NaN === NaN)	//false

console.log(Object.assign({name:'joey'},{name:'chandler',age:12}))

let o1 = {name:'san'}
let o2 = {arr:[1,2,3]}
console.log(Object.setPrototypeOf(o1,o2)) //设置原型对象
console.log(Object.getPrototypeOf(o1))	//读取原型对象
```

##### 两种方式引入js 模块化文件

```javascript
<script type="module">
    import {xxx} from 'xxx'
</script>

<script src="./xx" type="module"></script>
```





### ES7

> Array.prototype.includes
>
> **  指数运算符

```javascript
console.log(arr.includes(2))
console.log(2 ** 10)
```



### ES8

> **async**
>
> 1. async 函数的返回值为promise 对象
> 2. promise 对象的返回结果由async 函数执行的返回值决定
>
> **await**
>
> 1. await 必须卸载async 函数中
> 2. await右侧表达式一般为promise对象
> 3. await 返回的是 promise 成功的值
> 4. await的promise失败，就会抛出异常，需要用try..catch捕获处理

##### 新增API

```js
Object.keys() //获取对象所有的键
Object.values() //获取对象所有的值
Object.entries()  //获取对象所有的键值对

new Map(Object.entries())
```

```javascript
let obj = {name:'joey',age:123}
console.log(Object.keys(obj))
console.log(Object.values(obj))
console.log(Object.entries(obj))

let m = new Map(Object.entries(obj)) //对象 ==》 map
console.log('m ',m)
console.log('name',m.get('name'))

console.log('desc',Object.getOwnPropertyDescriptors(obj)) //获取对象描述
```



### ES9

##### 针对对象的rest参数和扩展运算符

> rest参数与spread运算符在ES6 中引入，但只针对数组，ES9 中为对象提供像数组一样的rest和spread

```javascript
function rest({name,age,sex,...rest}){
  console.log(name,age,sex)
  for(let item in rest){
    console.log(rest[item])
  }
}
rest({
  name:'joey',
  sex:'male',
  age:9,
  school:'asdf',
  address:'fdsdf'
})
let spread1 = {name:'joey',age:4}
let spread2 = {address:'new york'}
let spread3 = {name:'chandler'}
let spread = {...spread1,...spread2,...spread3}
console.log(spread)
```

##### 命名捕捉分组

```javascript
let str = '<a href="http://www.atguigu.com">guigu</a>'
// let reg = /<a href="(.*)">(.*)<\/a>/
let reg = /<a href="(?<url>.*)">(?<text>.*)<\/a>/   //分组
const result = reg.exec(str)
```

##### 反向断言

```javascript
let pattern = 'JS898989hello123world'
let regExp = /\d+(?=w)/ //正向断言
console.log(regExp.exec(pattern))

regExp = /(?<=o)\d+/   //反向断言
console.log(regExp.exec(pattern))
```



### ES10

##### Object.fromEntries

> 接收一个二维数组，或map参数，转成一个对象，obj[0]作为对象名，obj[1]作为值，或键名作为对象名，键值作为对象值

```js
let fromE = Object.fromEntries([[1,2],[5,6]])
console.log(fromE)

fromE = new Map()
fromE.set('name','joey')
console.log(Object.fromEntries(fromE))
```

> 与Object.entries功能相反，Object.entries将一个对象转换为一个二维数组

##### flat

> 将一个多维数组降维,默认深度为1

### ES11

##### 私有属性

```js
class female{
  name
  #age //私有属性
  #weight
  constructor(name,age,weight){
    this.name = name
    this.#age = age
    this.#weight = weight
  }
  intro(){
    console.log(this.name,this.#age,this.#weight)
  }
}
```

##### Promise.allSettled

> 接收一个promise数组，返回值为成功状态，
>
> 和Promise.all的区别：Promise.all的成功取决于数组中所有元素是否都是成功的

```js
let pro1 = new Promise((resolve,reject) =>{
  setTimeout(()=>{resolve('hello')},1000)
})
let pro2 = new Promise((resolve,reject) =>{
  setTimeout(()=>{reject('joey')},1000)
})
console.log(Promise.allSettled([pro1,pro2]))
```

##### 批量匹配

```js
let html = `<ul>
              <li>
                <p>肖申克的救赎</p>
                <p>上映日期：1999-9-9</p>
              </li>
              <li>
                <p>阿甘正传</p>
                <p>上映日期：2003-8-21</p>
              </li>
            </ul>`
//s 匹配所有 g 全局匹配
let pat = /<li>.*?<p>(?<file>.*?)<\/p>.*?<p>(?<time>.*?)<\/p>.*?<\/li>/sg
console.log(html.matchAll(pat))
console.log([...html.matchAll(pat)])

res ================>
Object [RegExp String Iterator] {}
[
  [
    '<li>\n' +
      '                <p>肖申克的救赎</p>\n' +
      '                <p>上映日期：1999-9-9</p>\n' +
      '              </li>',
    '肖申克的救赎',
    '上映日期：1999-9-9',
    index: 19,
    input: '<ul>\n' +
      '              <li>\n' +
      '                <p>肖申克的救赎</p>\n' +
      '                <p>上映日期：1999-9-9</p>\n' +
      '              </li>\n' +
      '              <li>\n' +
      '                <p>阿甘正传</p>\n' +
      '                <p>上映日期：2003-8-21</p>\n' +
      '              </li>\n' +
      '            </ul>',
    groups: [Object: null prototype] { file: '肖申克的救赎', time: '上映日期：1999-9-9' }
  ],
  [
    '<li>\n' +
      '                <p>阿甘正传</p>\n' +
      '                <p>上映日期：2003-8-21</p>\n' +
      '              </li>',
    '阿甘正传',
    '上映日期：2003-8-21',
    index: 125,
    input: '<ul>\n' +
      '              <li>\n' +
      '                <p>肖申克的救赎</p>\n' +
      '                <p>上映日期：1999-9-9</p>\n' +
      '              </li>\n' +
      '              <li>\n' +
      '                <p>阿甘正传</p>\n' +
      '                <p>上映日期：2003-8-21</p>\n' +
      '              </li>\n' +
      '            </ul>',
    groups: [Object: null prototype] { file: '阿甘正传', time: '上映日期：2003-8-21' }
  ]
]

```

##### 可选链操作符

```js
let option = {
  db:{
    config:'localhost'
  }
}
console.log(option && option.db && option.db.config) //localhost config未定义时返回一个undefined ,接收参数时使用，类似于java中避免空指针异常的解决方法
console.log(option?.db?.config) //可选链操作符
```

##### 动态import

```js
//import() 返回的值是promise对象
import('./app').then(module =>{
  console.log(module.sayGood())
})
```

##### BigInt

长整型

##### globalThis

始终指向全局对象
