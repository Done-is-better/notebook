 ##### 可以给变量指定类型

```typescript
let a: number
//赋值
let b:string = 'hello'
```

**给函数指定类型**

```typescript
function sum(a:number, b:number):number{
    return a+b;
}
```

##### TS 中的类型

![image-20220124000613782](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20220124000613782.png) 

```typescript

let sex: 'male' | 'female' //sex只能取其一，类似于定义一个常量
let a: string | number //联合类型
```

> 声明变量时如果不指定类型， TS 解析器自动判断变量的类型为 any 

> **any 和 unknow 的区别**
>
> any 类型的引用可以赋值给任意变量
>
> unknow 类型的引用不能*<a>直接</a>*赋值给其他变量(除了any)，实质上是一个类型安全的 any

```typescript
//进行类型检查后，unknow可以赋值给其他变量
let d:unknown = 'hello'
let e:string;
if( typeof e === 'string') e = d
//通过类型断言后，unknown 也可以赋值给其他变量
e = d as string 
e = <string> d
```

```typescript
/*void 和 never*/
//void 返回结果为空
function fun():void{
    
}
//never 永远不会有返回结果
function fun2():never{
    throw new Error('wrong')
}
```

```typescript
//object
let a: object
//object 类型较多
a = {}
a = []
a = function (){}
//使用{} 可以做更多限制
let b:{name: string, age?:number} //? 可写可不写
let c:{name: string, [propName: string]: any} //任意长度属性
```

```typescript
//function
let a : Function 
//函数结构类型声明
let b: (num1:number, num2:number) => number
b = function(a,b):number{
    return a + b;
}
```



```typescript
//Array
let arr : string[]
arr = ['hello','world']
let array : Array<string>;
array = ['hello','world']
```

```typescript
//tuple
let tup = [string, string]
tup = ['hello','world']
```

```typescript
//enum
enum Gender {
    MALE,FEMALE
}
let person : {name:string, gender:Gender}
person = {name:'chandler',gender:Gender.MALE}
```

##### 自定义类型

```typescript
type myType = 1 | 2 | 3 | 4;
let k : myType
k = 2
```



#### 编译选项

> tsc xx.ts   	编译
>
> tsc xx.ts -w    实时编译
>
> tsc -init 			生成json配置文件 ===》    tsc          全部编译

```json
/*tsconfig.json 配置项*/
{
    "include":[  //指定要编译的文件
        "./src/**/*"   //** 表示任意目录， * 表示任意文件
    ],
    "exclude":[],
    "extends":"./configs/base",   //定义被继承的配置文件，包含base.json 所有配置信息
    "files" : [  //指定被编译的文件列表，只有需要编译的文件少时才会使用
    	"core.ts",
    	"sys.ts"
    ],
	"compilerOptions":{ //编译器配置选项
        "target": "ES6",  //指定es 版本
        "module":"commonjs", // 指定使用的模块化规范
        "lib":["dom"], // 指定项目中使用的库
        "outDir":"./dist", // 指定编译后的文件目录
        "outFile": './dist/app.js', //所有全局作用域中的代码会合并到同一个文件
        "allowJs": false, // 是否对JS文件编译
        "checkJs":false,  // 检查JS是否符合语法规范
        "removeComments":false, // 是否移除注释
        "noEmit": false, //不生成编译后的文件
        "noEmitOnError": false, // 当有错误时不生成编译后的文件
        "alwaysStrict":true, //编译后的文件使用严格模式
        "noImplicitAny":true, // 不允许隐式的使用 any
        "noImplicitThis": true, // 不允许不明确类型的 this.
        "strictNullChecks":true // 严格的检查空值
    }
}
```



#### 使用webpack 打包ts 代码

> 使用npm 安装
>
> npm i -D webpack webpack-cli typescript ts-loader

[尚硅谷TypeScript教程（李立超老师TS新课）_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1Xy4y1v7S2?p=13)



### 面向对象

#### 类

```typescript
class Person{
    name:string = 'joey';
    static age:number = 12; //静态属性，通过Person.age 访问
    readonly hobby:string = 'sing'; //只读
    static readonly xx:string = 'xx'; //静态只读属性
    hello(){
        console.log('hello')
    }
}
```

##### 继承

```typescript
class Animal {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
    sayHello() {
        console.log('hello');
    }
}
class Dog extends Animal {
    constructor(name, age, hobby) {
        super(name, age);
        this.hobby = hobby;
    }
    sayHello() {
        super.sayHello();
        console.log(`${this.name} say hello to you`);
    }
}
```

##### 抽象类

```typescript
abstract class Animal{
    name: string;
    constructor(name:string){
        this.name = name
    }
    abstract sayHello('sdf'):void;
}
```

##### 接口

```typescript
//1.有自定义类型功能
type myType = {
  name:string,
  age:number
}
interface myInterface {
  name:string,
  age:number
}
let test :myType = {name:'joey',age:12}
let t :myInterface = {name:'joey',age:12}
//2.对类结构限制
interface myInter {
  name:string
  sayHello():void
}
class myInterImpl implements myInter{
  name:string
  constructor(name:string) {
    this.name = name
  }
  sayHello(){
    console.log('hello')
  }
}
```

##### 属性的封装

```typescript
class Cat extends Animal{
  private hobby :string
  constructor(name:string,age:number,hobby:string) {
    super(name,age);
    this.hobby = hobby
  }
  getHobby():string{
    return this.hobby;
  }
  setHobby(value:string):void{
    this.hobby = value
  }
  getName():string{
    return this.name
  }
  public setAge(value:number):void{
    this.age = value
  }
  getAge():number{
    return this.age
  }
}
console.log('=====================')
let cat = new Cat('tom',4,'eating')
console.log(cat)
console.log('read ',cat.getHobby())
cat.setHobby('sleeping')
console.log('set ',cat.getHobby())
console.log('name === ',cat.getName())
cat.setAge(2)
console.log('age set ',cat.getAge())
```

```typescript
//可以将属性的声明放到构造方法中
class C{
    constructor(private name:string){}
}
let c = new C();
```

##### 泛型

```typescript
function fu<T>(name:T):T{
  return name
}
console.log(fu('hello'))
console.log(fu<string>('joey'))

interface inter{
  length: number
}
function func<T extends inter>(a: T):number{
  return a.length
}
class hello implements inter{
  length: number;
  constructor(length:number) {
    this.length = length
  }
}
console.log(func<hello>({length:8}))
```

**______++++++**

> !  空抑制符

