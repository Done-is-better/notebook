#### 定义

> Promise 是JS中进行异步编程的新的解决方案（旧方案是回调函数）

**<a>Promise支持链式调用，可以解决回调地狱问题</a>**

回调地狱：回调函数嵌套调用，外部回调函数异步执行的结果是嵌套的回调执行的条件

![image-20220123190105802](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20220123190105802.png)



#### 状态

实例对象中的一个属性 **PromiseState**

pending 为决定的

resolved/fullfilled 成功

rejected 失败

另一个属性 **PromiseResult**

保存对象成功、失败的结果

resolve/reject

#### 工作流程

![image-20220123192403046](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20220123192403046.png)

#### API

> **<a>Promise 构造函数</a>**: Promise(excutor){}
>
> excutor: 同步执行 <a>(resolve,reject) =>{}</a>
>
> resolve: 内部定义成功时调用的函数
>
> reject: 内部定义失败是调用的函数 
>
> <a>**Promise.prototype.then**</a>
>
>  
>
> <a>**Promise.prototype.catch**</a> 
>
> 只定义失败时的回调函数



> **<a>Promise.resolve()</a>**
>
> 如果传入的参数为  非Promise 类型的对象，返回的结果为成功的promise 对象
>
> 如果传入的参数为 Promise 对象，则参数的结果决定 resolve 结果

> **<a>Promise.reject()</a>**
>
> 返回一个失败的 promise 对象
>
> 传入任何参数，返回的结果都为失败的promise(包括成功的promise)

>**<a>Promise.all()</a>:** (promises) =>{} 
>
>promises: 包含n 个promise 的数组
>
>返回一个新的 promise, 只有所有 promise 都成功时才成功

> **<a>Promise.race</a>:** (promises) =>{}
>
> promises: 包含n 个promise 的数组
>
> 返回一个新的 promise ,第一个完成的 promise 的结果状态就是最终的结果状态

#### 改变promise状态 和 指定回调函数 的执行顺序

![image-20220123195902305](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20220123195902305.png)

##### 中断 promise 链

方法：返回一个<a>pending</a> 状态的 promise

```javascript
p.then(value =>{
    return new Promise(() =>{})
})
```

![image-20220123201943870](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20220123201943870.png)

