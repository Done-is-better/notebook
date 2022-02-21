### 2.1Bean的配置

> Bean的本质就是Java中的类，而Spring中的Bean其实就是对实体类的引用，来生产Java类的对象，从而实现生产和管理Bean。

> Spring容器支持两种格式的文件:<a>Properties</a>和<a>XML</a>文件。
>
> 实际开发中，最常用的是XML格式的配置方式，这种配置方式是通过XML文件来注册并管理Bean之间的依赖关系。

> XML配置文件的根元素是<beans>,<beans>中包含多个<bean>子元素，每个<bean>子元素定义了一个Bean,并描述该bean如何被转配到Spring容器中。

> **bean的常用属性**
>
> <a>id</a> : 是一个Bean的唯一标识符，Spring容器对Bean的配置、管理都通过该属性来完成。
>
> <a>name</a>  : Spring容器同样可以通过该属性对容器中的Bean进行配置和管理，name属性中可以为Bean指定多个名称，每个名称之间用逗号或分号隔开。
>
> <a>class</a>  : 该属性指定了Bean的具体实现类，使用类的全限定名。
>
> <a>scope</a>  : 用来设定Bean实例的作用域，其属性有：<a>singleton,prototype,request,session,global,Session,application,websocket</a> ,默认值是singleton. 

### Bean的实例化

> 实例化Bean由三种方式：
>
> 构造器实例化(常用)、静态工厂方式实例化、实例工厂方式实例化

```java
//构造器实例化
//beans1.xml文件放在类文件所在的包下
public class InstanceTest {
    public static void main(String[] args){
        String path = "com\\example\\springchapter02\\beans1.xml";
        ApplicationContext context = new ClassPathXmlApplicationContext(path);
        Beans1 beans1 =(Beans1) context.getBean("beans1");
        System.out.println(beans1);
    }
}

/*
 注意：ClassPathXmlApplicationContext(path)是从classes文件夹下获取xml文件，可以将xml文件拷贝到class目录下，或更改output path到src下，避免异常。
*/
```



#### 2.2.2 静态工厂实例化

```java
//提供一个静态方法
public class Bean2 {
    public static Bean2 createBean(){
        return new Bean2();
    }
}
```

```xml
    <bean id="bean2" class="com.example.springchapter02.static_factory.Bean2" factory-method="createBean"></bean>

```

#### 2.2.3 实例工厂方式实例化

> 实例工厂采用直接创建Bean实例的方式，在配置文件中，通过factory-bean属性配置一个实例工厂，然后使用factory-method属性确定使用工厂中的那个方法。

```java
//实例方法
public class Bean3 {
    public Bean3(){
        System.out.println("bean3 创建中");
    }
    public Bean3 createBean(){
        return new Bean3();
    }
}
```

```xml
<bean id="bean3Factory" class="com.example.springchapter02.factory.Bean3"></bean>
<bean id="bean3" factory-bean="bean3Factory" factory-method="createBean">
</bean>
```

#### 2.3.1 作用域的种类

> **Spring 4.3 为Bean的实例提供了7种作用域**
>
> <a>singleton</a>  使用singleton定义的Bean在Spring容器中只有一个实例，这是Spring容器默认的作用域。
>
> <a>prototype</a> 每次通过Spring容器获取的prototype定义的Bean时，容器都将创建一个新的Bean实例。
>
> <a>request</a> 在一次HTTP请求中，容器会返回该Bean的同一个实例。对不同的HTTP请求则会产生一个新的Bean，而且该Bean仅在当前的HTTPRequest 内有效。
>
> <a>session</a> 在一次HTTP Session中，容器会返回该Bean的同一个实例。对不同的HTTP请求则会产生一个新的Bean，而且该Bean仅在当前HTTPSession内有效。
>
> <a>globalSession</a> 在一个全局HTTP Session 中，容器会返回该Bean的同一个实例，仅在使用protlet上下文时有效。
>
> <a>appllication</a> 为每个ServletContext对象创建一个实例。仅在Web相关的ApplicationContext中有效。
>
> <a>websocket</a> 为每个websocket对象创建一个实例。仅在Web相关的ApplicationContext中有效。



#### 2.3.2 singleton作用域

> singleton是Spring容器的默认作用域，当Bean的作用域为singleton时，Spring容器就只会存在一个共享Bean实例。singleton作用域对于无会话状态的Bean(如Dao组件，Service组件)，是最亮相的选择。

```xml
<bean id="scope" class="com.example.springchapter02.scope.Scope" scope="singleton"></bean>
```

#### 2.3.3 prototype作用域

> 对需要保持会话状态的Bean(如Struts2 的Action类) 应该使用prototype作用域。在使用prototype作用域时，Spring容器会为每个对该Bean的请求都创建一个新的实例。

```xml
<bean id="scope" class="com.example.springchapter02.scope.Scope" scope="prototype"></bean>
```



### 2.4 Bean的生命周期

Spring容器可以管理Bean部分作用域的生命周期。

> **singleton作用域**
>
> Spring 容器可以管理singleton作用域的Bean生命周期，在此作用域下，Spring能够精确地知道Bean何时被创建，何时初始化完成，何时被销毁。

> **prototype作用域**
>
> prototype作用域的Bean，Spring只负责创建Bean的实例后，Bean的实例就交给客户代码来管理，Spring容器不再跟踪其 生命周期。

![image-20211031130450979](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211031130450979.png)

```xml
<!--life.xml-->
   <!-- scope="singleton",默认情况下spring全程跟踪bean的生命周期-->
    <bean id="life" class="com.example.springchapter02.life.Life" init-method="initMethod" destroy-method="destoryMethod"></bean>

```

```java
ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("com/example/springchapter02/life/life.xml");
//ClassPathXmlApplicationContext提供了关闭容器的方法，这样bean的销毁方法才会被调用。
 context.close();
```



### 2.5 Bean的装配方式

> **什么是Bean的装配方式**
>
> Bean的装配方式可以理解为依赖关系注入，Bean的装配方式即Bean依赖注入的方式。Spring容器支持多种形式的Bean的装配方式，如基于XML的装配、基于注解的装配（最常用）和自动装配。

#### 2.5.1 基于XML的装配

![image-20211031132749747](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211031132749747.png)



```java

package com.example.springchapter02.assemble;

import java.util.List;

/**
 * @author chixinyu
 * @version 2021/10/31 13:31
 */
public class User {
    private String name;
    private Integer password;
    private List<String> list;

    public User() {
    }

    public User(String name, Integer password, List<String> list) {
        this.name = name;
        this.password = password;
        this.list = list;
    }
    public void setName(String name) {
        this.name = name;
    }
    public void setPassword(Integer password) {
        this.password = password;
    }
    public void setList(List<String> list) {
        this.list = list;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", password=" + password +
                ", list=" + list +
                '}';
    }
}
```

```xml
 <!-- 构造注入-->
    <bean id="user" class="com.example.springchapter02.assemble.User">
        <constructor-arg index="0" value="tom"/>
        <constructor-arg index="1" value="12345"/>
        <constructor-arg index="2">
            <list>
                <value>"constructor1"</value>
                <value>"constructor2</value>
            </list>
        </constructor-arg>
    </bean>
 <!--设值注入-->
    <bean id="user1" class="com.example.springchapter02.assemble.User">
        <property name="name" value="chandler"></property>
        <property name="password" value="12444"></property>
        <property name="list">
            <list>
                <value>"constructor1"</value>
                <value>"constructor2"</value>
            </list>
        </property>
    </bean>
```



#### 基于Annotation的装配

基于XML的装配可能会导致XML配置文件过于臃肿，给后续维护和升级带来一定的困难。为此，Spring提供了对Annotation技术的全面支持。

![image-20211031140019738](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211031140019738.png)



```java
//UserDao的实现类
@Repository("userDao")
public class UserDaoImpl implements UserDao{

    @Override
    public void say() {
        System.out.println("userdao say");
    }
}
//UserService的实现类
@Service("userService")
public class UserServiceImpl implements UserService{
    @Resource(name="userDao")
    private UserDao userDao;
    @Override
    public void save() {
        this.userDao.say();
        System.out.println("useservice save");
    }
}
//UserController
@Controller("userController")
public class UserController {
    @Resource(name="userService")
    private UserService userService;
    public void save(){
        this.userService.save();
        System.out.println("userController save");
    }
}
```

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd">
    <!--使用context命名空间，在配置文件中开启相应的注解处理器-->
    <context:annotation-config/>
    <!--定义三个bean 实例-->
    <bean id="userDao" class="com.example.springchapter02.annotation.UserDaoImpl"></bean>
    <bean id="userService" class="com.example.springchapter02.annotation.UserServiceImpl"></bean>
    <bean id="userController" class="com.example.springchapter02.annotation.UserController"></bean>

</beans>
```

> **使用context命名空间，通知Spring扫描指定包下所有Bean类，进行注解解析。** 
>
> <a><context:component-scan base-package="com.example.springchapter02.annotation"/></a>
>
> 



#### 自动装配

> 自动装配，就是将一个Bean自动的注入到其他Bean的Property中，Spring的<bean>元素中包含一个autowire属性，可以通过设置autowire的属性值来自动装配Bean。

> **autowire 的五个值**
>
> <a>default</a> 由<bean> 的上级标签<beans>的default-autowire属性值确定。如 <beans default-autowire="byName">
>
> <a>byName</a> 根据属性名自动装配。容器将根据名称查找与属性完全一致的Bean，并将其属性自动装配。
>
> <a>byType</a> 根据属性的数据类型自动装配，如果一个Bean的数据类型兼容另一个Bean中属性的数据类型，就自动装配。
>
> <a>constructor</a> 根据构造函数参数的数据类型，进行byType模式的自动装配。
>
> <a>no</a> 默认情况下，不使用自动装配，Bean依赖必须通过ref元素定义。