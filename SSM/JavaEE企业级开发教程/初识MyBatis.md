 ### 6.1 什么是MyBatis

MyBatis 是一个支持普通SQL查询、存储过程以及高级映射的持久层框架。

> MyBatis框架也被称为<a>**ORM(Object/Relation Mapping)**</a> ,即对象关系映射框架。ORM就是一种为了解决面向对象与关系型数据库中数据类型不匹配的技术，它通过描述Java对象与数据库表之间的映射关系，自动将Java应用程序中的对象持久化到关系型数据库的表中。

> **Hibernate与MyBatis的区别**
>
> <a>**Hibernate**</a> 
>
> - Hibernate是一个全表映射的框架。
> - 通常开发者只需定义好持久化对象数据到数据库表的映射关系，就可以通过Hibernate提供的方法完成持久层操作。
> - 开发者不需要熟练地掌握SQL语句的编写，Hibernate会根据制定的存储逻辑，自动的生成对应的SQL，并调用JDBC接口来执行，开发效率会高于MyBatis.
> - Hibernate的缺点：如多表关联时，对SQL查询的支持较差；更新数据时，需要发送所有的字段；不支持存储过程；不能通过优化SQL来优化性能等。

> <a>**MyBatis**</a>
>
> - MyBatis是一个半自动映射的框架
> - “半自动”是相对于Hibernate全表映射而言的，MyBatis需要手动匹配提供POJO、SQL和映射关系，而Hibernate只需要提供POJO和映射关系即可。
> - 与Hibernate相比，虽然使用MyBatis手动编写SQL要比使用Hibernate的工作量大，但MyBatis可以配置动态SQL并优化SQL，可以通过配置决定SQL的映射规则，还支持存储过程等。对于一些复杂的和需要优化性能的项目来说，使用MyBatis更合适。

### MyBatis的工作原理

![image-20211103221033655](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211103221033655.png)

```java
public class Customer {
    private int id;
    private String userName;
    private String jobs;
    private String phone;
//...setter
}
```

```pro
<!--log4j.properties-->
log4j.rootLogger=ERROR, stdout
log4j.logger.com.szjm.springchapter06=DEBUG
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n
```

```xml
<!--mybatis-config.xml-->
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--配置环境，默认的id为mysql-->
    <environments default="mysql">
        <!--配置id为mysql的数据库环境-->
        <environment id="mysql">
            <!--使用jdbc的事务管理-->
            <transactionManager type="JDBC"/>
            <!--数据库连接池-->
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mydb"/>
                <property name="username" value="root"/>
                <property name="password" value="421510"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <!--配置Mapper的位置，可以配置多个Mapper-->
        <mapper resource="com/szjm/springchapter06/pojo/Customer-Mapper.xml"></mapper>
        <mapper resource="com/szjm/springchapter06/pojo/Customer.xml"></mapper>
    </mappers>
</configuration>
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.szjm.springchapter06.pojo.Customer">
    <select id="selectCustomerById" parameterType="Integer" resultType="com.szjm.springchapter06.pojo.Customer">
        select * from t_customer where id = #{id}
    </select>
</mapper>
```

```java
 @Test
    public void findCustomerByIdTest() throws Exception {
        String config = "mybatis-config.xml";
        InputStream input = Resources.getResourceAsStream(config);
        SqlSessionFactory ssf = new SqlSessionFactoryBuilder().build(input);
        SqlSession sqlSession = ssf.openSession();
        Customer c =(Customer) sqlSession.selectOne("com.szjm.springchapter06.pojo.Customer.FindCustomerById",1);
        System.out.println(c.toString());
        sqlSession.close();
    }
```

+++

```xml
<!--模糊查询-->
<select id="selectCustomerByName" parameterType="String" resultType="com.szjm.springchapter06.pojo.Customer">
    select * from t_customer where name like '%${value}%' <!--只能写成value-->
</select>
<!--放置SQL 注入-->
<select id="selectCustomerByName2" parameterType="String" resultType="com.szjm.springchapter06.pojo.Customer">
    select * from t_customer where name like concat('%',#{username},'%') <!--可以随便命名-->
</select>
```

#### 添加操作

```xml
<insert id="addCustomer" parameterType="com.szjm.springchapter06.pojo.Customer">
        insert into t_customer (username,jobs,phone) values(#{username},#{jobs},#{phone})
    </insert>
```

```java
@Test
    public void addCustomerTest() throws Exception{
        String config = "mybatis-config.xml";
        InputStream input = Resources.getResourceAsStream(config);
        SqlSessionFactory ssf = new SqlSessionFactoryBuilder().build(input);
        SqlSession sqlSession = ssf.openSession();
        Customer customer = new Customer();
        customer.setUserName("tom");
        customer.setJobs("cook");
        customer.setPhone("1234");
        int row = sqlSession.insert("com.szjm.springchapter06.pojo.Customer.addCustomer",customer);
        if(row>0){
            System.out.println("插入"+row+"条记录");
        }else{
            System.out.println("失败");
        }
        sqlSession.close();
    }
```

#### 6.4.3 更新客户信息

```xml
<update id="updateCustomer" parameterType="com.itheima.po.Customer">
	update t_customer set username = #{username},jobs=#{jobs},phone=#{phone} where id =#{id}
</update>
```

```java
 @Test
    public void updateCustomerTest() throws Exception{
        String config = "mybatis-config.xml";
        InputStream input = Resources.getResourceAsStream(config);
        SqlSessionFactory ssf = new SqlSessionFactoryBuilder().build(input);
        SqlSession sqlSession = ssf.openSession();
        Customer customer = new Customer();
        customer.setId(4);
        customer.setUserName("tom");
        customer.setJobs("cook");
        customer.setPhone("1234");
        int row = sqlSession.update("com.szjm.springchapter06.pojo.Customer.updateCustomer",customer);
        if(row>0){
            System.out.println("修改"+row+"条记录");
        }else{
            System.out.println("失败");
        }
        sqlSession.commit();//增删改都涉及事务。
        sqlSession.close();
    }
```

#### 删除客户信息

```xml
<delete id="deleteCustomer" parameterType="Integer">
	delete from t_customer where id=#{id}
</delete>
```

