### 9.1关联关系概述

> 针对多表之间的操作，MyBatis提供了关联映射，通过关联映射可以很好的处理对象与对象之间的关联关系。



### 9.2 一对一

> \<resultMap>元素中包含一个<a>\<association></a> 子元素，MyBatis通过该元素处理一对一的关联关系。

> **在\<association>元素中，可以配置以下属性：**
>
> <a>**property**</a> 指定映射到的实体类对象属性，与表字段一一对应
>
> <a>**column**</a> 指定表中对应的字段
>
> <a>**javaType**</a> 指定映射到实体对象属性的类型
>
> <a>**select**</a> 指定引入嵌套查询的子SQL语句，该属性用于关联映射中的嵌套查询
>
> <a>**fetchType**</a> 指定在关联查询时是否启用延迟加载。该属性有<a>lazy</a> 和<a>eager</a> 两个属性值，默认值为<a>lazy</a> ,即默认关联映射延迟加载。

> **MyBatis延迟加载的配置**
>
> 使用MyBatis的延迟加载在一定程度上可以降低运行消耗并提高查询效率。MyBatis默认没有开启延迟加载，需要在核心配置文件中进行配置：
>
> ```xml
> <setting>
> 	<setting name="lazyLoadingEnabled" value="true"/>
>     <setting name="aggressiveLazyLoading" value="false"/>
> </setting>
> ```
>
> 在映射文件中，\<association>元素和\<collection>元素中都默认配置了延迟加载属性，即默认属性<a>fetchType="lazy" (fetchType="eager"表示立即加载)</a> ,所以配置文件中开启延迟加载后，无需在映射文件中再做配置。 



```xml
<mapper namespace="com.itheima.mapper.PersonMapper">
	<!--嵌套查询：通过执行另外一条SQL语句来返回预期的特殊类型-->
    <select id="findPersonById" parameterType="Integer" resultMap="IdCardWithPersonResult">
    	select * from tb_person where id = #{id}
    </select>
    <resultMap type="Person" id="IdCardWithPersonResult">
    	<id property="id" column="id"/>
        <result property="name" column="name"/>
        <result property="age" column="age"/>
        <result property="sex" column="sex"/>
        <!--一对一：association使用select 属性引入另外一条SQL语句-->
        <association property="card" column="card_id" javaType="IdCard" select="com.itheima.mapper.IdCardMapper.findCodeById"></association>
    </resultMap>
    
    <!--嵌套结果:使用嵌套结果映射来处理重复的联合结果的子集-->
    <select id="findPersonById2" parameterType="Integer" resultMap="IdCardWithPersonResult2">
    	select p.*,idcard.code from tb_person p,tb_idcard idcard where p.card_id=idcard.id and p.id=#{id}
    </select>
    <resultMap type="Person" id="IdCardWithPersonResult2">
    	<id property="id" column="id"/>
        <result property="name" column="name"/>
        <result property="age" column="age"/>
        <result property="sex" column="sex"/>
        <association property="card" javaType="IdCard">
        	<id property="id" column="card_id"/>
            <result property="code" column="code"/>
        </association>
    </resultMap>
</mapper>
```

```xml
<mapper namespace="com.itheima.mapper.IdCardMapper">
	<select id="findCodeById" parameterType="Integer" resultType="IdCard">
    	select * from tb_idcard where id = #{id}
    </select>
</mapper>
```



### 9.3 一对多

> <a>\<resultMap></a>元素中还包含一个<a>\<collection></a> 子元素，MyBatis通过该元素来处理一对多的关联关系。
>
> <a>\<collection></a> 子元素的属性大部分与<a>\<association></a> 元素相同，但还包含一个特殊属性<a>ofType</a> :
>
> <a>**ofType**</a> 与<a>javaType</a>属性对应，用于指定实体对象中集合类属性包含的元素类型。

```xml
<!--mybatis-config.xml-->
<setting>
	...
</setting>
<typeAliases>
	<package name="com.itheima.po"/>
</typeAliases>
```



```xml
<mapper namespace="com.itheima.mapper.UserMapper">
	<!--一对多：查看某一用户及其关联的订单信息  注意：当关联查询出的列名相同，需要使用别名区分-->
    <select id="findUserWithOrders" parameterType="Integer" resultMap="UserWithOrdersResult">
    	select u.*,o.id as orders_id,o.number from tb_user u,tb_orders o where u.id = o.user_id and u.id=#{id}
    </select>
    <resultMap type="User" id="UserWithOrdersResult">
    	<id property="id" column="id"/>
        <result property="username" column="username"/>
        <result property="address" column="address"/>
        <!--一对多关联映射：collection 
			用户持久化类中提供了属性:private List<Orders> orderList及setter
			ofType:表示属性集合中元素的类型，List<Orders>属性即Orders类
		-->
        <collection property="ordersList" ofType="Orders">
        	<id property="id" column="orders_id"/>
            <result property="number" column="number"/>
        </collection>
    </resultMap>
</mapper>
```



### 9.4 多对多

> 在MyBatis中，多对多的关联关系查询，同样可以使用<a>\<collection></a> 元素进行处理。

```xml
<!--OrdersMapper.xml-->
<mapper namespace="com.itheima.mapper.OrdersMapper">
	<!--多对多嵌套查询：通过执行另外一条SQL映射语句来返回预期的特殊类型-->
    <select id="findOrdersWithProduct" parameterType="Integer" resultMap="OrdersWithProductResult">
    	select * from tb_orders where id = #{id}
    </select>
    <resultMap type="Orders" id="OrderWithProductResult">
    	<id property="id" column="id"/>
        <result property="number" column="number"/>
        <collection property="productList" column="id" ofType="Product" select="com.itheima.mapper.ProductMapper.findProductById">
        </collection>
    </resultMap>
    <!--多对多嵌套结果：查询某订单及其关联的商品详情-->
    <select id="findOrdersWithProduct2" parameterType="Integer" resultMap="OrdersWithProductResult2">
    	select o.*,p.id as pid,p.name,p.price from tb_orders o,tb_product p, tb_ordersitem as oi where oi.orders_id=o.id and oi.product_id= p.id and o.id=#{id}
    </select>
    <resultMap type="Orders" id="OrderWithProductResult2">
    	<id property="id" column="id"/>
        <result property="number" column="number"/>
        <collection property="productList" ofType="Product">
        	<id property="id" column="pid"/>
            <result property="name" column="name"/>
            <result property="price" column="price"/>
        </collection>
    </resultMap>
</mapper>
```

```xml
<!--ProductMapper.xml-->
<mapper namespace="com.itheima.mapper.ProductMapper">
	<select id="findProductById" parameterType="Integer" resultType="Product">
    	select * from tb_product where id in ( select product_id from tb_ordersitem where orders_id = #{id})
    </select>
</mapper>
```

