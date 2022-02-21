

#### 1. 常用的Composition API

 ##### setup

> setup 函数的两种返回值：
>
> 1. 若返回一个对象，则对象中的属性、方法，在模板中可以直接使用。
> 2. 若返回一个渲染函数：则可以自定义渲染内容。
>
> **<a>setup 不能是一个async函数，因为返回值不再是return 的对象，而是promise ,模板看不到return对象中的属性。后期可以返回一个Promise实例，但需要Suspense和异步组件配合</a>**

##### ref函数

用于定义一个响应式的数据

> 对于基本类型的数据，响应式使用Object.defineProperty() 的getter 和setter 完成
>
> 对于对象类型的数据，内部使用Vue3的一个新函数 reactive函数



##### reactive函数

> 用于定义一个对象类型的响应式数据

##### 响应式原理

> **Vue2 实现响应式**
>
> == 使用Object.defineProperty实现响应式 ==
>
> 缺点：无法监测到添加的属性和删除的属性
>
> **Vue3实现响应式**
>
> *使用window.Proxy* 



##### 计算属性

```vue
//简写形式
import {computed} from 'vue'
let fullName = computed(() =>{
  return person.firstName+'-'+person.lastName
})
```

```vue
//全写
let fullName = computed({
      get: () => {return person.firstName + '-'+person.lastName},
      set: (value) => {
        let arr = value.split('-')
        person.firstName = arr[0]
        person.lastName = arr[1]
      }
    })
```



##### watch

> 监视ref 管理的属性，oldValue正常, watch函数配置项: 监视对象，handler, 配置对象

```javascript
import {watch} from 'vue'
watch(fullName, (newValue,oldValue) =>{
  console.log('oldValue ',oldValue)
  console.log('newValue ',newValue)
},{immediate: true, deep: true})
```

> 监视reactive时，oldValue 无法显示

```javascript
watch(person, (n,o) =>{
  console.log('value',n,o)
},{immediate: true})
```

> 注意： vue3的watch 是强制开启的，无法关闭

```javascript
//深度监视某个属性,避免监视整个对象造成性能降低(watch只能监视对象、数组、ref管理的简单数据)
watch(() => person.age,(newValue) =>{
      console.log('person age === ',newValue)
    })
```



##### watchEffect

> 监视涉及的属性

```javascript
watchEffect(() =>{
  const a = person.age
  const name1 = name
  console.log('@@@@ ',a,name1)
})
```

##### Vue3 与Vue2 生命周期钩子对应关系

| Vue2                 | Vue3            |
| -------------------- | --------------- |
| beforeCreate/created | setup()         |
| beforeMount          | onBeforeMount   |
| mounted              | onMounted       |
| beforeUpdate         | onBeforeUpdate  |
| updated              | onUpdated       |
| beforeUnmount        | onBeforeUnmount |
| unmounted            | onUnmounted     |

###### 实现代码复用

![image-20220122141305731](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20220122141305731.png)



##### toRef 和toRefs

> 将一个对象的属性变成ref ，而不产生新数据(ref)

![image-20220122144922874](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20220122144922874.png)

#### 2.其他API

##### shallowReactive 和shallowRef

> shallowReactive 只考虑第一层数据的响应式
>
> shallowRef 不做对象的响应式
>
> *应用场景*
>
> 1. 如果有一个对象数据，结构比较深，但变化时只是外层属性变化 ==》 shallowReactive
> 2. 如果有一个对象数据，后续功能不修改该对象中的属性，而是生成心的对象来代替 ==》 shallowRef.

##### readOnly 和shallowReadOnly



##### toRaw 和 markRaw

> **toRaw**
>
> 作用： 将一个由 reactive 生成的响应式对象转为普通对象
>
> 使用场景： 用于读取响应式对象对应的普通对象，对这个普通对象的所有操作，不回引起页面更新

> **markRaw**
>
> 作用： 标记一个对象，使其永远不会成为响应式对象
>
> 应用场景：
>
> 1. 有些值不应该设置为响应式的，如第三方类库
> 2. 当渲染具有不可变数据源的大列表时，跳过响应式转换可以提高性能



##### customRef

> 创建一个自定义的ref, 并对其依赖项跟踪和更新触发进行显示控制

```javascript
//实现数据防抖 
function myRef(value){
      let timer
      return customRef((track, trigger) =>{
        return {
          get(){
            track()
            return value
          },
          set(newValue){
            clearTimeout(timer)
            timer = setTimeout(() =>{
              value = newValue
              trigger()
            },500)
          }
        }
      })
    }
    let custom = myRef('hello')
```



##### provide 与 inject

> 作用： 实现祖孙间的组件通信（父子间一般用props，provide也可以使用）

![image-20220123143329899](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20220123143329899.png)

#### 3.新的组件

##### Fragment

> 在Vue3中，组件可以没有根标签，内部会将多个标签包含在一个Fragment虚拟元素中
>
> 好处： 减少标签层级，减小内存占用

##### teleport

```html
<!--组件传送-->
<teleport to="#teleport">
  <div v-if="isShow" style="background-color: peru">
    <h2>HELLO WORLD</h2>
  </div>
</teleport>
```

##### suspense

> 等待异步组件时渲染一些额外的内容

```javascript
import {defineAsyncComponent} from 'vue'
const Child = defineAsyncComponent(() => import('@/components/Child'))
components:{Child}
```

```html
<Suspense>
      <template v-slot:default>
        <Child/>
      </template>
      <template v-slot:fallback>
        <h3>加载中</h3>
      </template>
    </Suspense>
```

#### 4.其他改变

| 全局API(Vue)             | 实例API(app)                |
| ------------------------ | --------------------------- |
| Vue.config.xxx           | app.config.xxx              |
| Vue.config.productionTip | 移除                        |
| Vue.component            | app.component               |
| Vue.directive            | app.directive               |
| Vue.mixin                | app.mixin                   |
| Vue.use                  | app.use                     |
| Vue.prototype            | app.config.globalProperties |

![image-20220123160659278](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20220123160659278.png)





#### 注意点

> 通过props 传值不能直接访问，需要用toRefs转成ref,通过value 获取

```vue
props:{
  doChange:{type:Function}
},
setup(props){
  const {doChange} = toRefs(props)
  function doHidden(){
    doChange.value(true)
  }
```

