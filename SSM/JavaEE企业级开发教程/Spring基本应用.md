 #### 1.1什么是Spring

> Spring 是分层的JavaSE/EE full-stack 轻量级开发框架，以IoC<a>(Inverse of Control控制翻转)</a> 和AOP<a>(Aspect Oriented Programming)</a> 为内核，使用基本的JavaBean来完成以前只可能由EJB完成的工作，取代了EJB的臃肿、低效的开发模式。

实际开发中，通常服务器端在采用三层体系架构，分别为表示层Web、业务逻辑层Service、持久层Dao,Spring对每一层都提供了技术支持。 

在表示层提供了与Struts等框架的整合。

在业务逻辑层可以管理事务、记录日志等。

在持久层可以整合Hibernate、JdbcTemplate等技术。

#### 1.1.2 Spring 框架的优点

**Spring具有简单、可测试和松耦合等特点。Spring不仅可以用于服务端开发，也可以用于任何Java应用的开发中**

> **Spring 框架的七大优点**
>
> 1. 非侵入式设计
> 2. 方便解耦、简化开发
> 3. 支持AOP
> 4. 支持声明式事务处理
> 5. 方便程序测试
> 6. 方便集成各种优秀框架
> 7. 降低Java EE API 的使用难度

#### 1.1.3 Spring的体系结构

> 1. **Core Container(核心容器):** <a>Beans,Core,Context,SpEL</a> 
>
>    Beans提供了BeanFactory，Spring将管理的对象成为Bean.
>
>    Core提供了Spring框架的基本组成部分，包括IoC和DI功能。
>
>    Context建立在Core和Beans模块的基础上，是访问定义和配置的任何对象的媒介。
>
>    SpEL :Spring3.0后新增模块，是运行时查询和操作对象图的强大表达式语言。
>
> 2. **Date Access/Integration(数据访问/继承)**：<a>JDBC,ORM,OXM,JMS,Transactions</a>
>
>    JDBC:提供了一个JDBC的抽象层，大幅减少了在开发中对数据库操作的编码。
>
>    ORM:对流行的对象关系映射API,包括JPA,JDO和Hibernate提供了集成层支持。
>
>    OXM:提供了一个支持对象/XML映射的抽象层实现，如JAXB、Castor、XMLBeans、JiBX和XStream.
>
>    Transactions:支持对实现特殊接口及所有POJO类的编程和声明式的事务管理。
>
>    JMS:指Java消息传递服务，包含使用和产生信息的特性，自4.1版本后支持与Spring-message模块的集成
>
> 3. **Web**: <a>WebSocket,Servlet,Web,Protlet</a>
>
>    Web:提供了基本的Web开发集成特性，如：多文件上传、使用Servlet监听器来初始化IoC容器及Web应用上下文。
>
>    Portlet:提供了在protlet环境中使用MVC实现，类似Servlet模块的功能。
>
> 4. **其他模块**：<a>AOP,Aspects,Instrumentation,Messaging,Test</a>
>
>    AOP：提供了面向切面编程实现，允许定义方法拦截器和切入点，将代码按照功能进行分离，以降低耦合性。
>
>    Aspects:提供了与AspectJ的集成功能，AspectJ是一个功能强大且成熟的面向切面编程(AOP)框架。
>
>    Instrumentation:提供了类工具的支持和类加载器的实现，可以再特定的应用服务器中使用。
>
>    Messaging:Spring4.0以后新增的模块，提供了对消息传递体系结构和协议的支持。
>
>    Test:提供了对单元测试和集成测试的支持。

#### 1.2 Spring的核心容器

> Spring 容器会负责控制程序之间的关系，而不是由程序代码直接控制。Spring提供了两种核心容器，分别是**BeanFactory**和**ApplicationContext**

#### 1.2.1BeanFactory

创建BeanFactory实例时，需要提供Spring所管理容器的详细配置信息，这些信息通常采用XML文件形式来管理，其加载配置信息的语法为：

<a>BeanFactory beanFactory = new XmlBeanFactory(new FileSystemResource("F:/applicationContext.xml"))</a>

#### 1.2.2 ApplicationContext

ApplicationContext是BeanFactory的子接口，是另一种常用的Spring核心容器。由org.springframework.context.ApplicationContext接口定义，不仅包含了BeanFactory的所有功能，还添加了对国际化、资源访问、事件传播等方面的支持。创建ApplicationContext接口实例，通常采用两种方法：

1. **通过ClassPathXmlApplicationContext创建**

   <a>ApplicationContext applicationContext = new ClassPathXmlApplicationContext(String configLocation);</a>

   ClassPathXmlApplicationContext会从类路径classPath中找到指定的XML配置文件，找到并装载完成ApplicationContext的实例化工作。

2. **通过FileSystemXmlApplicationContext创建**

   <a>ApplicationContext applicationContext = new FileSystemXmlApplicationContext(String configLocation);</a>

   FileSystemXmlApplicationContext会从指定的文件系统路径中寻找指定的XML配置文件，找到并装载完成ApplicationContext的实例化工作。

在Java项目中，会通过ClassPathXmlApplicationContext类来实例化ApplicationContext容器。

在Web项目中，ApplicationContext容器的实例化工作会交由Web服务器来完成。

Web服务器实例化ApplicationContext容器时，通常会使用ContextLoaderListener来实现，此种方式只需要在web.xml中添加：

![image-20211029223755892](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211029223755892.png)

创建Spring容器后，就可以获取Spring容器中的Bean。Spring获取Bean的实例通常采用以下两种方法：

1. <a>Object getBean(String name)</a>

   根据容器中Bean的id或name 来获取指定的Bean，获取之后需要进行强制类型转换。

2. <a><T> T getBean(Class<T> requiredType)</a> 

   根据类的类型来获取Bean的实例。由于此方法为泛型方法，因此在获取Bean之后不需要强转。

```java
  //初始化spring容器，加载配置文件
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        //通过容器获取SubClass实例
        SubClass sc = (SubClass)context.getBean("instance01");
        sc.say();
```

#### 1.4.1 依赖注入(Dependency Injection)

> **IoC**
>
> 在使用Spring框架之后，对象的实例不再由调用者来创建，而是由Spring容器来创建，Spring容器会负责控制程序之间的关系，而不是由调用者的程序代码直接控制。这样程序权由应用代码转移到Spring容器，控制权发生了翻转，这就是控制反转。

> **DI**
>
> 从Spring容器的角度看，Spring容器负责将被依赖对象赋值给调用者的成员变量，这相当于为调用者注入了它依赖的实例。

依赖注入的实现方式：setter注入、构造方法注入

```java
//使用setter注入
public class UserServiceImpl implements UserService{
    private SubClass sc;

    public void setSc(SubClass sc) {
        this.sc = sc;
    }

    @Override
    public void say() {
        System.out.println("DI say");
        sc.say();
    }
}

//applicationContext.xml:
	<bean id="instance01" class="com.szjm.demo02web.SubClass"></bean>

    <bean id="instance02" class="com.szjm.demo02web.UserServiceImpl">
        <property name="sc" ref="instance01"></property>
    </bean>
```

