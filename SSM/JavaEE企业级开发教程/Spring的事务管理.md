 ### 5.1 Spring 事务管理概述

> 实际开发中，操作数据库时都会涉及到事务管理问题，为此，Spring提供了专门用于事务处理的API。
>
> Spring的事务管理简化了传统的事务管理流程，并在一定程度上减少了开发者的工作量。

在Spring的所有JAR包中，包含一个spring-tx的JAR包，该包就是Spring提供的用于处理事务管理的依赖包。该JAR包的org.springframework.transaction包中，由三个接口文件platformTransationManager,

TransactionDefinition 和transactionStatus.



#### 5.1.1 事务管理的核心接口

> **Platform TransactionManager**
>
> *Platform TransactionManager*接口是Spring提供的平台事务管理器，主要用于管理事务。该接口提供三个事务操作的方法：
>
> <a>TransactionStatus getTransaction(TransactionDefinition definition)</a> 用于获取事务状态信息
>
> <a>void commit(TransactionStatus status)</a> 用于提交事务
>
> <a>void rollback(TransactionStatus status)</a> 用于回滚事务

> <a>PlatformTransactionManager</a> 接口只是事务管理的接口，具体如何管理事务由它的实现类完成。
>
> 常见实现类：
>
> <a>org.springframework.jdbc.datasource.DataSourceTransactionManager</a> 用于配置JDBC数据源的事务管理器
>
> <a>org.springframework.orm.hibernate4.HibernateTransactionManager</a> 用于配置Hibernate的事务管理器
>
> <a>org.springframework.transaction.jta.JtaTransactionManager</a> 用于配置全局事务管理器。

当低层采用不同的持久层技术时，系统只需使用不同的platformtransactionmanager实现类即可。



> **TransactionDefinition接口是事务定义的对象，该对象定义了事务的规则，并提供了获取事务相关信息的方法：**
>
> <a>String getName()</a> 获取事务的名称
>
> <a>int getIsolationLevel()</a> 获取事务的隔离行为
>
> <a>int getPropagationBehavior()</a> 获取事物的 传播行为
>
> <a>int getTimeout()</a> 获取事务的超时时间
>
> <a>boolean isReadObly()</a> 获取事务是否只读

事务的传播行为是指在同一个方法中，不同操作前后使用的事务。传播行为有很多种：

![image-20211103013145466](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211103013145466.png)

> <a>在事务的管理过程中，传播行为可以控制是否需要创建事务以及如何创建事务，通常情况下，数据的查询不会影响原数据的改变，所以不需要进行事务管理，而对于数据的插入、更新和删除，必须进行事务管理。**如果没有指定事务的传播行为，Spring默认传播行为是REQUIRED**</a> 



> **TransactionStatus**
>
> TransactionStatus接口是事务的状态，它描述了某一时间点上事务的状态信息。该接口包含6个方法：
>
> <a>void flush()</a> 刷新事务
>
> <a>boolean hasSavepoint()</a> 获取是否存在保存点
>
> <a>boolean isCompleted</a> 获取事务是否完成
>
> <a>boolean isNewTransaction()</a> 获取事务是否为新事务
>
> <a>boolean isRollbackObly()</a> 获取事务是否回滚
>
> <a>void setRollbackOnly()</a> 设置事务回滚



#### 5.1.2 事务管理的方式

> **Spring事务管理分为两种方式：**
>
> <a>**编程式事务管理:**</a> 通过编写代码实现事务管理，包括定义事务的开始、正常执行后的事务提交和异常时的事务回滚。
>
> <a>**声明式事务管理:**</a> 通过AOP技术实现的事务管理，主要思想是将食物作为一个“切面”代码单独编写，然后通过AOP技术将事务管理的“切面”织入到业务目标类中。  

*声明式事务管理最大的优点在于开发者无需通过编程的方式来管理事务，只需在配置文件中进行相关的事务规则声明，就可以将事务应用到业务逻辑中。*



### 5.2 声明式事务管理

*Spring声明式的事务管理可以通过两种方式来实现，一种是基于XML的方式，一种是基于Annotation的方式。* 



#### 5.2.1 基于XML的声明式事务管理

> *基于XML方式的声明式事务是在配置文件中通过\<tx:advice> 元素配置事务规则来实现的。当配置了事物的增强处理后，就可以通过编写的AOP配置，让Spring自动对目标生成代理。\<tx:advice>元素及其子元素:*
>
> ![image-20211103015949556](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211103015949556.png)



> **配置\<tx:advice>元素的重点是配置\<tx:method>子元素，\<tx:method>元素常用属性：**
>
> <a>**name**</a> 该属性为必选属性，指定了与事务属性相关的方法名。其属性值支持使用通配符，如“\*”，“get\*","handle\*","\*Order"等。
>
> <a>**propagation**</a> 用于指定事务的传播方式，默认值是REQUIRED。
>
> <a>**isolation**</a> 该属性用于指定事务的隔离级别，属性值可以是DEFAULT、READ_UNCCOMMITTED、READ_COMMITTED、REPEATABLE_READ和SERIALIZABLE,默认值是DEFAULE。
>
> <a>**read-only**</a> 用于指定事务是否只读，默认值是false.
>
> <a>**timeout**</a> 用于指定事务超时的时间，默认值为-1，即永不超时。
>
> <a>**rollback-for**</a> 用于指定触发事务回滚的异常类，在指定多个异常类时，异常类之间以英文逗号分割。
>
> <a>**no-rollback-for**</a> 用于指定不触发事务回滚的异常类，在指定多个异常类时，异常类之间以英文逗号分割。 

```java
//模拟异常
 @Override
    public void transfer(String outUser, String inUser, int salary) {
        jdbcTemplate.update("update account set balance = balance + ? where username = ?",salary,inUser);
        //模拟突发异常
        int i = 1/0;
        jdbcTemplate.update("update account set balance = balance - ? where username =?",salary,outUser);
    }
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">
    <!--配置数据源-->
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <!--数据库驱动-->
        <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
        <!--链接数据库的url-->
        <property name="url" value="jdbc:mysql://localhost:3306/mydb?useUnicode=true&amp;characterEncoding=utf8"></property>
        <!--连接数据库的用户名-->
        <property name="username" value="root"/>
        <!--密码-->
        <property name="password" value="421510"/>
    </bean>
    <!--配置JDBC模板-->
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <!--默认必须使用数据源-->
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <bean id="accountDao" class="com.szjm.springchapter05.dao.AccountDaoImpl">
        <property name="jdbcTemplate" ref="jdbcTemplate"></property>
    </bean>

    <!--事务管理器，依赖数据源-->
   <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"></property>
    </bean>
    <!--编写通知：对事务进行增强，需要编写切入点和具体执行事务细节-->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <tx:method name="*" propagation="REQUIRED" isolation="DEFAULT" read-only="false"></tx:method>
        </tx:attributes>
    </tx:advice>
    <!--编写AOP让Spring对目标生成代理，需要使用AspectJ表达式-->
    <aop:config>
        <aop:pointcut expression="execution(* com.szjm.springchapter05.*.*(..))" id="txPointCut"/>
        <!--将融入点通知整合-->
        <aop:advisor advice-ref="txAdvice" pointcut-ref="txPointCut"/>
    </aop:config>
</beans>
```



#### 5.2.2 基于Annotation方式的声明式事务

> **1. 在Spring容器中注册事务注解驱动**
>
> <a><tx:annotation-driven transaction-manager="transactionManager"/></a> 
>
> **2. 在需要事务管理的类或方法上使用@Transactional注解**
>
> 如果将注解添加在Bean类上，则表示事务的设置对整个Bean类所有方法都起作用；如果将注解添加在Bean类中的某个方法上，则表示事物的设置只对该方法有效。

> **@Transactional的参数配置**
>
> <a>**value**</a> 用于指定需要使用的事务管理器，默认为“”，别名为<a>transactionManager</a> 
>
> <a>**transactionManager**</a> 指定事物的限定符值，可用于确定目标事务管理器，匹配特定的限定值(或者Bean的name值)，默认为“”，别名为<a>value</a> 
>
> <a>**isolation**</a> 用于指定事物的隔离级别，默认为<a>Isolation.DEFAULT</a> ,即低层事务隔离级别
>
> <a>**noRollbackFor**</a> 用于指定遇到特定异常时强制不会滚事务。
>
> <a>**noRollbackForClassName**</a> 用于指定事务的传播行为，默认为<a>Propagation.REQUIRED</a> 
>
> <a>**propagation**</a> 用于指定事务的传播行为，默认为<a>Propagation.REQUIRED</a> 
>
> <a>**read-only**</a> 用于指定事务是否只读，默认为false
>
> <a>**rollbackFor**</a> 用于指定遇到特定异常时强制回滚事务。
>
> <a>**rollbackForClassName**</a> 用于指定遇到的特定的多个异常时强制回滚事务。其属性值可以指定多个异常类名。
>
> <a>**timeout**</a> 用于指定事务的超时时长，默认为<a>TransactionDefinition.TIMEOUT_DEFAULT</a> ,即底层事务系统的默认时间。

```xml
<!--事务管理器，依赖数据源-->
   <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"></property>
    </bean>
    <!--注册事务管理器驱动-->
    <tx:annotation-driven transaction-manager="transactionManager"/>
```

```java
@Transactional(propagation= Propagation.REQUIRED,isolation= Isolation.DEFAULT,readOnly=false)
    public void transfer(String outUser, String inUser, int salary) {
        jdbcTemplate.update("update account set balance = balance + ? where username = ?",salary,inUser);
        //模拟突发异常
        int i = 1/0;
        jdbcTemplate.update("update account set balance = balance - ? where username =?",salary,outUser);
    }
```

