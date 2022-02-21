### 10.1 整合环境搭建

![image-20211107144305824](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211107144305824.png)

![image-20211107144356989](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211107144356989.png)

![image-20211107144416020](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211107144416020.png)

#### 10.1.2 编写配置文件

```xml
<!--加载配置文件-->
<context:property-placeholder location="classpath:db.property"/>
 
<!--事务管理器，依赖于数据源-->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTramsactionManager">
	<property name="dataSource" ref="dataSource"/>
</bean>
<!--开启事务注解，注册事务管理器驱动-->
<tx:annotation-driven transaction-manager="transactionManager"/>
<!--MyBatis与Spring整合-->
<!--配置MyBatis工厂-->
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
	<!--注入数据源-->
    <property name="dataSource" ref="dataSource"/>
    <!--指定核心配置文件位置-->
    <property name="configLocation" value="classpach:mybatis-config.xml"></property>
</bean>
```

![image-20211107150547564](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211107150547564.png)

