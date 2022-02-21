### 4.1 Spring JDBC

> Spring的JDBC模块负责数据库资源管理和错误处理，简化了开发人员对数据库的操作。
>
> 针对数据库的操作，Spring框架提供了JdbcTemplate类，该类是Spring框架数据抽象层的基础。JdbcTemplate类是Spring JDBC的核心类。
>
> ![image-20211102205807517](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211102205807517.png)
>
> JdbcAccessor为JdbcTemplate类提供一些访问数据库的公共属性：
>
> <a>DataSource:</a> 其主要功能是获取数据库连接，还可以引入对数据库连接的缓冲池和分布式事务的支持，可以作为访问数据库资源的标准接口。
>
> <a>SQLExceptionTranslator:</a> 该接口负责对SQLException进行转译工作。通过必要的设置获取SQLExceptionTranslator中的方法，可以使JDBCTemplate在需要的时候处理SQLException时，委托SQLExceptionTranslator实现类来完成相关的转译工作。
>
> JdbcOperations接口定义了在JdbcTemplate类中可以使用的操作集合，包括添加、修改、查询和删除等操作。

#### 4.1.2 Spring JDBC的配置

> Spring JDBC 模块主要由四个包组成，分别是core(核心包)、dataSource(数据源包)、object(对象包)和support(支持包)。

> <a>core</a> 包含了JDBC的核心功能，包括JdbcTemplate类，SimpleJdbcInsert类，SimpleJdbcCall类以及NamedParameterJdbcTemplate类。
>
> <a>dataSource</a> 访问数据源的实用工具类，它有多种数据源的实现，可以再JavaEE容器外部测试JDBC代码。
>
> <a>object</a> 以面向对象的方式访问数据库，允许执行查询并将结果作为业务对象，可以在数据表的列和业务对象的属性之间映射查询结果。
>
> <a>support</a> 包含了core和object包的支持类，如，提供异常转换功能的SQLException类。 

### 4.2 Spring JDBCTemplate的常用方法

> <a>execute(String sql )</a> 用于执行sql
>
> <a>update()</a> 用于执行插入、更新和删除操作
>
> <a>query()</a> 用于查询操作

```xml
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
```

```java
 public static void main(String[] args) {
        ApplicationContext context = new 		ClassPathXmlApplicationContext("applicationContext.xml");
        JdbcTemplate jdbc = (JdbcTemplate)context.getBean("jdbcTemplate");
        String sql = "create table account(id int primary key auto_increment,username char(30),password varchar(12))";
        jdbc.execute(sql);
    }
```

```java
//Junit4 测试
 @Test
    public void mainTest() {
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        JdbcTemplate jdbc = (JdbcTemplate)context.getBean("jdbcTemplate");
        String sql = "create table account(id int primary key auto_increment,username char(30),password varchar(12))";
        jdbc.execute(sql);
    }
```



#### 4.2.2 update()

> **update()方法可以完成插入，更新和删除数据库的操作**
>
> <a>int update(String sql)</a> 该方法是最简单的update方法重载形式，直接传入了sql语句，并返回受影响的行数。
>
> <a>int update(PreparedStatementCreator psc)</a> 执行从PreparedStatementCreator返回的语句，然后返回受影响的行数。
>
> <a>int update(String sql)</a> 通过PreparedStatementSetter 设置SQL语句中的参数，并返回受影响的行数。
>
> <a>int update(String sql ,Object...args)</a> 使用Object...设置SQL语句中的参数，要求参数不能为空，返回受影响的行数。 

```java
public class Account {
    private int id;
    private String name ;
    private String password;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    @Override
    public String toString() {
        return "Account{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", password='" + password + '\'' +
                '}';
    }
}
```

```java
public interface AccountDao {
    int addAccount(Account account);
    int updateAccount(Account account);
    int deleteAccount(int id);
}

+++
    
package com.szjm.springchapter04enhanced.dao;

import org.springframework.jdbc.core.JdbcTemplate;

/**
 * @author chixinyu
 * @version 2021/11/2 23:13
 */
public class AccountDaoImpl implements AccountDao{
    //声明JdbcTemplate属性及setter
    private JdbcTemplate jdbcTemplate;
    public void setJdbcTemplate(JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }

    @Override
    public int addAccount(Account account) {
        String sql = "insert into account(username,password) values(?,?)";
        //定义数组存放sql语句中的参数
        Object[] obj = new Object[]{
                account.getName(),account.getPassword()
        };
        int num = jdbcTemplate.update(sql,obj);
        return num;
    }

    @Override
    public int updateAccount(Account account) {
        String sql = "update account set username = ? ,password = ? where id=?";
        Object[] obj = new Object[]{
          account.getName(),account.getPassword(),account.getId()
        };
        int num = jdbcTemplate.update(sql,obj);
        return num;
    }

    @Override
    public int deleteAccount(int id) {
        String sql = "delete from account where id = ?";
        int num = jdbcTemplate.update(sql,id);
        return num;
    }
}

```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
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
    <bean id="accountDao" class="com.szjm.springchapter04enhanced.dao.AccountDaoImpl">
        <property name="jdbcTemplate" ref="jdbcTemplate"></property>
    </bean>
</beans>
```

```java
//测试
@Test
    public void accountTest(){
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        AccountDao ad = (AccountDao)context.getBean("accountDao");
        Account account = new Account();
        account.setName("chandler");
        account.setPassword("1234chan");
        int num = ad.addAccount(account);
        if(num > 0)
            System.out.println("添加成功！");
    }
```



#### 4.2.3 query()

> **JdbcTemplate类提供了大量的query()方法来处理数据库表的查询操作。**
>
> <a>List query(String sql,RowMapper rowMapper)</a> 执行String类型参数提供的SQL语句，通过RowMapper返回一个List类型的结果。
>
> <a>List query(String sql,PreparedStatementSetter pss,RowMapper rowMapper)</a> 根据String类型的参数提供的SQL语句创建PreparedStatement对象，通过RowMapper将结果返回到List中。
>
> <a>List query(String sql,Object[] args,RowMapper rowMapper)</a> 使用Object[]的值来设置SQL语句中的参数值，采用RowMapper回调方法可以直接返回List类型的数据。
>
> <a>queryForObject(String sql,RowMapper rowMapper,Object...args)</a> 将args参数绑定到SQL语句中，并通过RowMapper返回一个Object类型的单行记录。
>
> <a>queryForList(String sql,Object[] args,class<T> elementType)</a> 该方法可以返回多行数据的结果，但必须返回列表，elementType参数返回的是List元素类型。

```java
@Override
    public Account findAccountById(int id) {
        String sql = "select * from account where id = ?";
        //创建一个新的BeanPropertyRowMapper对象
        RowMapper<Account> rowMapper = new BeanPropertyRowMapper<>(Account.class);
        //将id 绑定到SQL语句中，通过RowMapper返回一个Object类型的单行记录
        return jdbcTemplate.queryForObject(sql,rowMapper,id);
    }

    @Override
    public List<Account> findAllAccount() {
        String sql = "select * from account";
        RowMapper<Account> rowMapper = new BeanPropertyRowMapper<>(Account.class);
        return jdbcTemplate.query(sql,rowMapper);
    }
```

```java
 @Test
    public void findAccountByIdTest(){
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        AccountDao ad = (AccountDao)context.getBean("accountDao");
        Account account = ad.findAccountById(4);
        System.out.println(account);
    }
    @Test
    public void findAllAccountTest(){
        ApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
        AccountDao ad = (AccountDao)context.getBean("accountDao");
        List<Account> list = ad.findAllAccount();
        for(Account account:list){
            System.out.println(account);
        }
    }
```

