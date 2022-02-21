 #### 3.1.1 Spring AOP简介

> <a>AOP</a> 全称<a>Aspect-Oriented Programming</a> ,即面向切面编程。它是面向对象(OOP)的补充。

> AOP 采用横向抽取机制，将分散在各个方法中的重复代码提取出来，然后在程序编译或运行时，再将这些提取出来的代码应用到需要执行的地方。
>
> 这种横向抽取机制，传统OOP思想无法办到，因为OOP只能实现父子关系的纵向继承的重用。

#### 3.1.2 AOP术语

> <a>Aspect</a> 封装的用于横向插入系统功能的类。
>
> <a>Joinpoint</a> 在程序执行过程中的某个阶段点。
>
> <a>Pointcut</a> 切面与程序流程的交叉点，即那些需要处理的连接点。
>
> <a>Advice(通知/增强处理)</a>  AOP框架在特定的切入点执行的增强处理，即在定义好的切入点处要执行的程序代码。可以将其理解为切面类中的方法。
>
> <a>Target Object</a> 指所有被通知的对象，也被称为增强对象。如果AOP框架采用的是动态的AOP实现，那么该对象就是一个被代理的对象。
>
> <a>Proxy</a> 将通知应用到目标对象之后，被创建的对象。 
>
> <a>Weaving(织入)</a> 将切面代码植入到目标对象上，从而产生代理对象的过程。   



#### 3.2.1 JDK动态代理

> JDK动态代理是通过java.lang.reflex.Proxy类来实现的，可以调用Proxy类的newProxyInstance()方法来创建代理对象。
>
> 对于使用业务接口的类，Spring默认使用JDK动态代理来实现AOP。

```java
//interface
public interface UserDao {
    void addUser();
    void deleteUser();
}
//UserDaoImpl
public class UserDaoImpl implements UserDao{
    @Override
    public void addUser() {
        System.out.println("添加用户");
    }
    @Override
    public void deleteUser() {
        System.out.println("删除用户");
    }
}
//MyAspect
//切面类
public class MyAspect {
    public void check_Permissioins(){
        System.out.println("检查权限");
    }
    public void log(){
        System.out.println("模拟记录日志");
    }
}

package com.szjm.springchapter03.dynamicproxy;

import com.szjm.springchapter03.aspect.MyAspect;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

/**
 * @author chixinyu
 * @version 2021/10/31 15:51
 */
public class JDKProxy implements InvocationHandler {
    private UserDao userDao;
    public Object createProxy(UserDao userDao){
        this.userDao = userDao;
        //类加载器
        ClassLoader classLoader = JDKProxy.class.getClassLoader();
        //被代理对象实现的所有接口
        Class<?>[] interfaces = userDao.getClass().getInterfaces();
        //使用代理类，进行增强，返回的是代理后的对象
        return Proxy.newProxyInstance(classLoader,interfaces,this);
    }
    /*
    * 所有动态代理类的方法调用，都交由invoke方法处理
    *proxy: 被代理后的对象
    * method:将要被执行的方法信息
    * args:执行方法时需要的参数。
    */
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        //声明切面
        MyAspect myAspect = new MyAspect();
        //前增强
        myAspect.check_Permissioins();
        //在目标类上调用方法，并传入参数
        Object obj = method.invoke(userDao,args);
        //后增强
        myAspect.log();
        return obj;
    }
}

//invoke test
public class JdkTest {
    public static void main(String[] args){
        //创建代理对象
        JDKProxy jdkProxy = new JDKProxy();
        //创建目标对象
        UserDao userDao = new UserDaoImpl();
        //从代理对象获取增强后的目标对象
        UserDao ud =(UserDao) jdkProxy.createProxy(userDao);
        ud.addUser();
        ud.deleteUser();
    }
}
```

#### 

#### 3.2.2 CGLIB代理

JDK动态代理的局限性：使用动态代理的对象必须实现一个或多个接口

> <a>CGLIB(Code Generation Library)</a> 是一个高性能开源代码生成包，采用低层的字节码技术，对指定的目标类生成一个子类，并对子类增强。

```java
public class MyProxy implements MethodInterceptor {
    /**
     *
     * @param proxy CGlib根据指定的父类生成的代理对象
     * @param method  拦截的方法
     * @param args  拦截的方法的参数数组
     * @param methodProxy 方法的代理对象，用于执行父类的方法
     */
    @Override
    public Object intercept(Object proxy, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
        //创建切面对象
        MyAspect myAspect = new MyAspect();
        //前增强
        myAspect.check_Permissioins();
        //目标方法执行
        Object obj = methodProxy.invokeSuper(proxy,args);
        //后增强
        myAspect.log();
        return obj;
    }
    public Object createProxy(Object obj){
        //创建一个动态代理类对象
        Enhancer enhancer = new Enhancer();
        //确定要增强的类，设置其父类
        enhancer.setSuperclass(obj.getClass());
        //添加回调函数
        enhancer.setCallback(this);
        return enhancer.create();
    }
}
```

```java
  public static void main(String[] args){
        MyProxy myProxy = new MyProxy();
        UserDao userDao = new UserDao();
        UserDao userDaoEnhanced = (UserDao)myProxy.createProxy(userDao);
        userDaoEnhanced.addUser();
        userDaoEnhanced.deleteUser();
    }
```



### 3.3 基于代理类的AOP实现

#### 3.3.1 Spring 的通知类型

> **Spring 按照通知在目标类方法的连接点位置，可以分为5种类型：**
>
> <a>org.aopalliance.intercept.MethodInterceptor(环绕通知)</a> 在目标方法执行前实施增强，可以应用于日志、事务管理等功能。
>
> <a>org.springframework.aop.MethodBeforeAdvice(前置通知)</a> 在目标方法执行前实施增强，可以应用于权限管理等功能。
>
> <a>org.springframework.aop.AfterReturningAdvice(后置通知)</a> 在目标方法执行后实施增强，可以应用于关闭流、上传文件、删除临时文件等功能。
>
> <a>org.springframework.aop.ThrowsAdvice(异常抛出通知)</a> 在方法抛出异常后实施增强，可以应用于处理异常记录日志等功能。
>
> <a>org.springframework.aop.IntroductionInterceptor(引介通知)</a> 在目标类中添加一些新的方法和属性，可以用于修改老版本程序。



#### 3.3.2 ProxyFactoryBean

> <a>ProxyFactoryBean</a> 是<a>FactoryBean</a> 接口的实现类，<a>FactoryBean</a> 负责实例化一个Bean，而<a>ProxyFactoryBean</a> 负责为其他Bean创建代理实例。在Spring中，使用ProxyFactoryBean是创建AOP代理的基本方式。

> **ProxyFactoryBean类的常用可配置属性如下：**
>
> <a>target</a> 代理的目标对象
>
> <a>proxyInterfaces</a> 代理要实现的接口，如果有多个接口，可以使用以下格式赋值：<list><value></value>...</list>
>
> <a>proxyTargetClass</a> 是否对类代理而不是接口，设置为true时，使用CGLIB 代理。
>
> <a>interceptorNames</a> 需要织入目标的Advice.
>
> <a>singleton</a> 返回的代理是否为单实例，默认为true
>
> <a>optimize</a> 设置为true时，强制使用CGLIB.

```java
public class MyAspect implements MethodInterceptor {

    @Override
    public Object invoke(MethodInvocation invocation) throws Throwable {
        check_permission();
        //执行目标方法
        Object obj = invocation.proceed();
        log();
        return null;
    }

    private void log() {
        System.out.println("模拟执行");
    }

    private void check_permission() {
        System.out.println("模拟执行验证");
    }
}
```

```xml
<!--切面类-->
    <bean id="myAspect" class="com.szjm.springchapter03.proxyfactorybean.MyAspect"></bean>
    <!--目标类-->
    <bean id="user" class="com.szjm.springchapter03.dynamicproxy.UserDaoImpl"></bean>
    <!--使用Spring代理工厂定义一个代理对象-->
    <bean id="userProxy" class="org.springframework.aop.framework.ProxyFactoryBean">
        <!--指定代理实现的接口-->
        <property name="interfaces" value="com.szjm.springchapter03.dynamicproxy.UserDao"></property>
        <!--指定目标对象-->
        <property name="target" ref="user"></property>
        <!--指定切面，织入环绕通知-->
        <property name="interceptorNames" value="myAspect"></property>
        <!--指定代理方式：true：使用cglib代理，false:使用jdk动态代理-->
        <property name="proxyTargetClass" value="true"></property>
    </bean>
```

```java
  public static void main(String[] args) {
        String xmlPath = "com/szjm/springchapter03/proxyfactorybean/applicationContext.xml";
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext(xmlPath);
        UserDao userDao = (UserDao)applicationContext.getBean("userProxy");
        userDao.addUser();
        userDao.deleteUser();
    }
```



### 3.4 AspectJ

> <a>AspectJ</a> 是一个基于Java语言的AOP框架，他提供了强大的AOP功能。Spring2.0以后，Spring引入了对AspectJ的支持，并允许直接使用AspectJ进行编程。新版本Spring框架，也建议使用AspectJ来开发AOP。
>
> 使用AspectJ实现AOP由两种方式：一种基于XML的声明式AspectJ，另一种是基于注解的声明式AspectJ。



#### 3.4.1 基于XML的声明式AspectJ

> 基于XML的声明式AspectJ是指通过XML文件来定义切面、切入点即通知，所有的切面、切入点和通知都必须定义在"<aop:config>" 元素内。

![image-20211101231213738](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211101231213738.png) 



> **1.配置切面**
>
> 在Spring的配置文件中，配置切面使用的是<aop:aspect>元素，该元素会将一个已经定义好的Spring Bean转换成切面Bean，所以要在配置文件中先定义一个普通的Spring Bean。
>
> 配置<aop:aspect>元素时，通常会指定id 和ref 两个属性。
>
> <a>id</a> 用于定义改切面的唯一标识名称。
>
> <a>ref</a> 用于引用普通的Spring Bean

> **2.设置切入点**
>
> 当<a>\<aop:pointcut></a> 元素作为\<aop:config>元素的子元素定义时，表示该切入点是全局切入点，它可以被多个切面共享；当\<aop:pointcut>元素作为\<aop:aspect>元素的子元素时，表示该切入点只是对当前切面有效。
>
> 定义\<aop:pointcut>元素时，通常指定id和expression两个属性。
>
> <a>id</a> 用于指定切入点的唯一标识名称。
>
> <a>expression</a> 用于指定切入点关联的切入点表达式。
>
> ***切入点表达式***
>
> <a>execution(* com.itheima.jdk.\*.\*(..))</a> :匹配com.itheima.jdk包下任意类的任意方法的执行。第一个\* 代表返回值类型为所有类型，com.itheima.jdk表示要拦截的包名，第二个\*表示所有的类，第三个\*表示所有的方法(注意空格)

> **3. 配置通知**
>
> 使用<a>\<aop:aspect></a> 的子元素可以配置5种常用通知，这5个子元素不支持使用子元素，但在使用时可以指定一些属性：
>
> <a>pointcut</a> 该属性用于指定一个切入点表达式，Spring将在匹配该表达式的连接点时织入该通知。
>
> <a>pointcut-ref</a> 该属性指定一个已经存在的切入点名称，如配置代码中的myPointCut。通常<a>pointcut</a> 和<a>pointcut-ref</a> 两个属性只需要使用其中之一。
>
> <a>method</a> 该属性指定一个方法名，指定将切面Bean中的该方法转换为增强处理。
>
> <a>throwing</a> 该属性只对<after-throwing>元素有效，它用于指定一个形参名，异常通知方法可以通过该形参访问目标方法所抛出的异常。
>
> <a>returning</a> 该属性只对<after-returning>元素有效，用于指定一个形参名，后置通知方法可以通过该形参访问目标方法的返回值。 

```java
package com.szjm.springchapter03.aspectJ;
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.ProceedingJoinPoint;

/**
 * @author chixinyu
 * @version 2021/11/2 7:19
 */
public class MyAspect {
    public void beforeAdvice(JoinPoint joinPoint){
        System.out.println("前置通知：模拟检查权限");
        System.out.println("增强目标："+joinPoint.getTarget());
        System.out.println("织入增强处理的方法："+joinPoint.getSignature().getName());
    }
    public void afterAdvice(JoinPoint joinPoint){
        System.out.println("后置通知：模拟打印日志");
        System.out.println("织入增强处理的方法："+joinPoint.getSignature().getName());
    }

    /**
     * ProceedingJoinPoint是JoinPoint的子接口，表示可以执行目标方法。
     * @param proceedingJoinPoint 必须接收一个参数，类型为ProceedingJoinPoint
     * @return 返回值必须是Object类型
     *
     */
    public Object aroundAdvice(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        System.out.println("环绕通知开始：模拟检察事务");
        Object obj = proceedingJoinPoint.proceed();
        System.out.println("环绕结束");
        return obj;
    }
    //异常通知
    public void myThrowing(JoinPoint joinPoint ,Throwable e){
        System.out.println("出错了："+e.getMessage());
    }
    //最终通知
    public void myFinal(){
        System.out.println("关闭流");
    }
}

```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
         http://www.springframework.org/schema/aop
         http://www.springframework.org/schema/aop/spring-aop.xsd">
    <!--目标类-->
    <bean id="user" class="com.szjm.springchapter03.dynamicproxy.UserDaoImpl"></bean>
    <!--切面-->
    <bean id="myAspect" class="com.szjm.springchapter03.aspectJ.MyAspect"></bean>
    <!--AOP编程-->
    <aop:config>
        <!--配置切面-->
        <aop:aspect ref="myAspect">
            <!--配置切入点-->
            <aop:pointcut expression="execution(* com.szjm.springchapter03.dynamicproxy.*.*(..))" id="myPoint"></aop:pointcut>
            <!--关联通知Advice和切入点pointcut-->
            <!--前置通知-->
            <aop:before method="beforeAdvice" pointcut-ref="myPoint"></aop:before>
            <!--后置通知，在方法返回之后执行，可以获取返回值。 returning属性：用于设置后置通知的第二个参数名称，类型是Object-->
            <aop:after-returning method="afterAdvice" pointcut-ref="myPoint" returning="joinPoint"></aop:after-returning>
            <!--环绕通知-->
            <aop:around method="aroundAdvice" pointcut-ref="myPoint"></aop:around>
            <!--异常通知-->
            <!--throwing属性：用于设置第二个参数的名称，类型为Throwable-->
            <aop:after-throwing method="myThrowing" pointcut-ref="myPoint" throwing="e"></aop:after-throwing>
            <!--最终通知 一定会执行-->
            <aop:after method="myFinal" pointcut-ref="myPoint"></aop:after>
        </aop:aspect>
    </aop:config>
</beans>
```



+++

#### 3.4.2 基于注解的声明式AspectJ

> **AspectJ框架为AOP的实现提供了一套注解，用以取代Spring配置文件中为实现AOP功能配置的代码臃肿：**
>
> ![image-20211102084320417](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211102084320417.png)

```java
package com.szjm.springchapter03.aspectJ.anotation;
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;
import org.springframework.stereotype.Component;

/**
 * @author chixinyu
 * @version 2021/11/2 7:19
 */
@Component
@Aspect
public class MyAspect {
    //切入点
    @Pointcut("execution(* com.szjm.springchapter03.dynamicproxy.*.*(..))")
    public void myPointCut(){}
    @Before(value="myPointCut()")
    public void beforeAdvice(JoinPoint joinPoint){
        System.out.println("前置通知：模拟检查权限");
        System.out.println("增强目标："+joinPoint.getTarget());
        System.out.println("织入增强处理的方法："+joinPoint.getSignature().getName());
    }
    @AfterReturning("myPointCut()")
    public void afterAdvice(JoinPoint joinPoint){
        System.out.println("后置通知：模拟打印日志");
        System.out.println("织入增强处理的方法："+joinPoint.getSignature().getName());
    }

    /**
     * ProceedingJoinPoint是JoinPoint的子接口，表示可以执行目标方法。
     * @param proceedingJoinPoint 必须接收一个参数，类型为ProceedingJoinPoint
     * @return 返回值必须是Object类型
     *
     */
    @Around("myPointCut()")
    public Object aroundAdvice(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        System.out.println("环绕通知开始：模拟检察事务");
        Object obj = proceedingJoinPoint.proceed();
        System.out.println("环绕结束");
        return obj;
    }
    //异常通知
    @AfterThrowing(value="myPointCut()",throwing="e")
    public void myThrowing(JoinPoint joinPoint ,Throwable e){
        System.out.println("出错了："+e.getMessage());
    }
    //最终通知
    @After("myPointCut()")
    public void myFinal(){
        System.out.println("关闭流");
    }
}

```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
         http://www.springframework.org/schema/beans/spring-beans.xsd
         http://www.springframework.org/schema/aop
         http://www.springframework.org/schema/aop/spring-aop.xsd
         http://www.springframework.org/schema/context
         http://www.springframework.org/schema/context/spring-context.xsd">
   <!--指定需要扫描的包，使注解生效-->
    <context:component-scan base-package="com.szjm.springchapter03"/>
    <!--启动基于注解的声明式AspectJ支持-->
    <aop:aspectj-autoproxy/>
</beans>
```

![image-20211102091917869](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211102091917869.png)

