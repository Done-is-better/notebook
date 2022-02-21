### 8.1 动态SQL中的元素

> MyBatis3采用了基于<a>OGNL</a>的表达式来完成动态SQL。

![image-20211107093324142](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211107093324142.png)

### 8.2 \<if>元素

![image-20211107093507309](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211107093507309.png)

### 8.3 \<choose>,\<when>,\<otherwise>元素

![image-20211107094855891](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211107094855891.png)

### 8.4 \<where>,\<trim>元素

> \<where>会自动判断SQL语句，只有\<where>内的判断条件成立时，才会在拼接SQL中加入where关键字，否则不会添加；还会去除多余的"and"或"or".

![image-20211107095758196](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211107095758196.png)

> \<trim>的作用是去除特殊的字符串，它的prefix属性代表语句的前缀，prefixOverrides属性代表需要去除的那些特殊的字符串，功能和\<where>基本是等效的。



### 8.5 \<set>元素

![image-20211107100526304](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211107100526304.png)



### 8.6 \<foreach>元素

![image-20211107101409661](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211107101409661.png)

> **\<foreach>中使用的几种属性**：
>
> <a>item</a> 配置的是循环中当前的元素。
>
> <a>index</a> 配置的是当前元素在集合的位置下标。
>
> <a>collection</a> 配置的list是传递过来的参数类型（首字母小写），它可以是array,list(或collection),map集合的键，POJO包装类中数组或集合类型的属性名等。
>
> <a>open</a> 和<a>close</a> 配置的是一什么符号将这些集合元素包装起来。
>
> <a>separator</a> 配置的是各个元素的间隔符。



### 8.7 \<bind>元素

![image-20211107102824932](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211107102824932.png)

> MyBatis的\<bind>元素可以通过OGNL表达式来创建一个上下文变量

![image-20211107103040467](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211107103040467.png)

