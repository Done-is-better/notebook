### 7.1.1 SqlSessionFactory

> - <a>**SqlSessionFactory**</a> 是MyBatis框架中十分重要的对象，是单个数据库映射关系经过编译的内存镜像，主要作用是创建SqlSession.
> - <a>**SqlSessionFactory**</a> 对象的实例可以通过<a>SqlSessionFactoryBuilder</a>a对象来构建，而<a>SqlSessionFactoryBuilder</a> 可以通过XML文件或一个预先定义好的Configuration实例构建出SqlSessionFactory的实例。

> <a>**SqlSessionFactory**</a> 对象时线程安全的，一旦被创建，在整个应用执行期间都会存在，建议使用单例模式构建<a>SqlSessionFactory</a> 实例。

#### 7.1.2 SqlSession

***封装了JDBC的Connection,使用后要及时关闭*** 

> <a>**SqlSession**</a> 是<a>MyBatis</a> 框架中另一个重要的对象，是应用程序与持久层执行交互操作的一个单线程对象，主要作用是执行持久化操作。
>
> 每一个线程都应该有一个自己的<a>SqlSession</a> 实例，并且该实例是不能被共享的。同时<a>SqlSession</a> 实习也是线程不安全的，因此使用范围最好在一次请求或一个方法中，<a>*决不能将其存放在一个类的静态字段、实例字段或任何类型的管理范围(如Servlet的HttpSession)中使用*</a>  

```java
//通过sqlSessionFactory创建sqlSession
String resource = "mybatis-config.xml";
InputStream inputStream = Resources.getResourceAsSteam(resource);
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
SqlSession sqlSession = sqlSessionFactory.openSession();
```



#### 7.2.1 主要元素

> 在MyBatis框架的核心配置文件中，<configuration>元素时配置文件的根元素，其他元素都在<configuration>元素内配置。

![image-20211106101251591](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211106101251591.png)



#### 7.2.2 \<properties>元素

> <properties>是一个配置属性的元素，该元素通常用来将内部的配置外在化，即通过外部的配置来动态的替换内部定义的属性。如：数据库的链接等属性，就可以通过典型的java属性文件中的配置来替换。

```pro
<!--db.properties-->
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/mybtais
jdbc.username=root
jdbc.password=root
```

```xml
<!--mybatis-config 读取db.properties-->
<properties resource="db.properties"/>
<environments default="mysql">
        <!--配置id为mysql的数据库环境-->
        <environment id="mysql">
            <!--使用jdbc的事务管理-->
            <transactionManager type="JDBC"/>
            <!--数据库连接池-->
            <dataSource type="POOLED">
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>
```

```properties
#db.properties
jdbc.driver=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/mydb
jdbc.username=root
jdbc.password=421510
```



#### 7.2.3 \<settings>元素

> 主要用于改变MyBatis运行时的行为，如开启二级缓存、开启延迟加载等



#### 7.2.4 \<typeAliases>

> <typeALiases>元素用于为配置文件中的Java类型设置一个简短的名字，即设置别名。别名的设置与XML配置相关，其使用的意义在于减少全限定类名的冗余。



> **\<typeAliases>元素的配置别名方法**
>
> \<typeAliases>
>
> ​	\<typeAlias alias="user" type="com.itheima.po.User"/>
>
> \</typeAlliases>
>
> **当POJO类过多时，可以通过自动扫描报的形式自定义别名：**
>
> \<typeAliases>
>
> ​	\<package name="com.xxx.xx"/>
>
> \</typeAliases>

![image-20211106105643856](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211106105643856.png)



单个配置

```xml
<!--mybatis-config.xml-->
<typeAliases>
	<typeAlias alias="customer" type="com.itherma.po.Customer"/>
</typeAliases>
```

```xml
<!--在CustomerMapper.xml中使用-->
<select id="findCustomerById" parameterType="Integer" resultType="customer">
	select * from xx where id = #{id}
</select>
```

+++

配置扫描包形式，别名就是类名首字母小写

```xml
<!--mybatis-config.xml-->
<typeAliases>
	<package name="com.itheima.po"/>
</typeAliases>
```

```xml
<!--在CustomerMapper.xml中使用-->
<select id="findCustomerById" parameterType="Integer" resultType="customer">
	select * from xx where id = #{id}
</select>
```



#### 7.2.5 \<typeHandler>

> <a>typeHandler</a> 的作用是将预处理语句中传入的参数从javaType(Java类型) 转换为jdbcType(jdbc类型)，或者从数据库取出结果时将jdbcType转换为javaType.
>
> \<typeHandler>元素可以在配置文件中注册自定义的类型处理器，它的使用方式有两种：
>
> ![image-20211106191142208](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211106191142208.png)



#### 7.2.6 \<objectFactory>

> MyBatis中默认的ObjectFactory的作用是实例化目标类，它既可以通过默认构造方法实例化，也可以在参数存在的时候通过参数构造方法来实例化。

![image-20211106191843689](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211106191843689.png)

![image-20211106191902400](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211106191902400.png)



#### 7.2.7 \<plugins>

> MyBatis允许在已映射语句执行过程中的某一点进行拦截调用，这种调用是通过插件来实现的。<plugins>元素的作用就是配置用户所开发的插件。



#### 7.2.8 \<environments>

> <a>\<environments></a> 元素用于对环境配置。MyBatis的环境配置实际上就是数据源的配置，可以通过\<environment>元素配置多种数据源，即配置多种数据库。

![image-20211106192459227](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211106192459227.png)

> 在MyBatis中，可以配置两种类型的事务管理器，分别是JDBC和MANAGED.
>
> <a>**JDBC:**</a> 自配置直接使用了JDBC的提交和回滚设置，它依赖于从数据源得到的链接来管理事物的作用域。
>
> <a>**MANAGED:**</a> 自配置从不提交或回滚一个链接，而是让容器来管理事物的整个生命周期。默认情况下，他会关闭连接，但一些容器并不希望这样，为此可以将<a>closeConnection</a> 属性设置为false来阻止它默认的关闭行为。
>
> **如果项目中使用的是Spring+ MyBatis，则没必要在MyBatis中配置事务管理器，因为实际开发中，会使用Spring自带的管理器来实现事务管理 ** 

> **数据源的配置**
>
> <a>**1.UNPOOLED:**</a> 配置此数据源类型后，在每次请求时会打开和关闭连接。它对没有性能要求的简单应用程序是一个很好的选择。在使用时需要配置5中属性：
>
> <a>*driver*</a> JDBC驱动的Java类的完全限定名
>
> <a>*url*</a>  数据库的URL地址
>
> <a>*username*</a> 登录数据库的用户名
>
> <a>*password*</a> 登录数据库的密码
>
> <a>*defaultTransactionIsolationLeave*</a> 默认的连接事务隔离级别
>
> <a>**2.POOLED**</a> 利用“池”的概念将JDBC连接对象组织起来，避免在创建新的连接实例时所需要初始化和认证的时间。这种方式使并发Web应用可以快速的响应请求，是当前流行的处理方式。在使用时可以配置更多的属性。
>
> ![image-20211106194337524](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211106194337524.png)
>
>  
>
> <a>**3.JNDI**</a> 可以在EJB或应用服务器等容器中使用。容器可以集中或在外部配置数据源，然后方式一个JDBI上下文的引用。在使用时需要配置两个属性：
>
> <a>initial_context</a> 此属性主要用于在<a>InitialContext</a> 中寻找上下文（即initialContext.lookup(initial_context)).该属性为可选属性，忽略时，data_scource属性直接从InitialContext中寻找。
>
> <a>data_source</a> 此属性表示引用数据元实例位置的上下文的路径。如果提交了initial_context配置，程序会在其返回的上下文中进行查找，如果没有提供，直接在InitialContext中查找。

 

#### 7.2.9 \<mappers>

> \<mappers>元素用于指定MyBatis映射文件的位置，一般可以使用一下4中方法引入映射器文件：
>
> **1.使用类路径引入**
>
> ```xml
> <mappers>
> 	<mapper resource="com/itheima/mapper/UserMapper.xml"/>
> </mappers>
> ```
>
> **2.使用本地路径引入**
>
> ```xml
> <mappers>
> 	<mapper url="file:///D:/com/itheima/mapper/UserMapper.xml"/>
> </mappers>
> ```
>
> **3.使用接口类引入**
>
> ```xml
> <mappers>
> 	<mapper class="com.itheima.mapper.UserMapper"/>
> </mappers>
> ```
>
> **4.使用包名引入**
>
> ```xml
> <mappers>
> 	<package name="com.itheima.mapper"/>
> </mappers>
> ```
>
> 



#### 7.3.1 主要元素

> 在映射文件中，\<mapper>元素是映射文件的根元素，其他元素都是它的子元素。

![image-20211106200424380](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211106200424380.png)



#### 7.3.2 \<select>

> <seslect>元素用来映射查询语句，他可以从数据库读取数据，并组装。

![image-20211106200727568](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211106200727568.png)



#### 7.3.3 \<insert>

> \<insert>元素用于映射插入语句，在执行完元素中定义的SQL语句后，会返回一个表示插入记录数的整数。

![image-20211106201009366](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211106201009366.png)

#### 7.3.4 \<update>和\<delete>

![image-20211106201423100](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211106201423100.png)

![image-20211106201438323](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211106201438323.png)



#### 7.3.5 \<sql>

> \<sql>元素的作用就是定义可重用的SQL代码片段，然后在其他语句中引用。

```xml
<!--定义一个包含id,username,jobs,phone字段的代码片段-->
<sql id="customerColumns">id,username,jobs,phone</sql>

<!--在其他语句中使用-->
<select id="findCusttomerById" parameterType="Integer" resultType="com.itheima.po.Customer">
	select <include refid="customerColumns"/>
    from t_customer where id = #{id}
</select>
```



#### 7.3.6 \<resultMap>

> \<resultMap>元素表示结果映射集，是MyBatis中最重要也是最强大的元素。主要作用是定义映射规则、级联更新以及定义类型转换器等。

![image-20211106202652852](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211106202652852.png)

```xml
<!--UserMapper.xml-->
<mapper namespace="com.itheima.po.User">
	<resultMap type="com.itheima.po.User" id="resultMap">
    	<!--主键-->
        <id property="id" column="t_id"/>
        <!--普通属性-->
        <result property="name" column="t_name"/>
        <result property="age" column="t_age"/>
    </resultMap>
    <!--字段名和属性名不匹配时，使用resultMap-->
    <select id="findAllUser" resultMap="resultMap">
    	select * from t_user
    </select>
</mapper>
```



