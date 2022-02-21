> **更新记录**
>
> 方式一：添加默认时间
>
> ![image-20211223153717590](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211223153717590.png)
>
> 方式二：代码层次
>
> ```java
> //添加注解
> @TableField(fill= FieldFill.INSERT)
>     private Date createTime;
>     @TableField(fill=FieldFill.INSERT_UPDATE)
>     private Date updateTime;
> ```
>
> ```java
> //配置handler
> @Component
> public class MyMetaObject implements MetaObjectHandler {
>     @Override
>     public void insertFill(MetaObject metaObject) {
>         this.setFieldValByName("createTime",new Date(),metaObject);
>     }
> 
>     @Override
>     public void updateFill(MetaObject metaObject) {
>         this.setFieldValByName("updateTime",new Date(),metaObject);
>     }
> }
> ```
>
> 

#### 乐观锁处理

1. 实体类添加注解

```java
@Version
private Integer version;
```

2. 编写配置类

```java
@Component
@EnableTransactionManagement
public class MyBatisPlusConfig {
    @Bean
    public OptimisticLockerInterceptor optimisticLockerIntercepter(){
        return new OptimisticLockerInterceptor();
    }
}
```

3. 模拟多线程操作乐观锁测试

```java
@Test
void optimisticLockerTest(){
    Users user = userList.selectById(19);
    Users user2 = userList.selectById(19);
    user.setName("zhangsan").setAddress("xxxx");
    user2.setName("chandler").setAddress("shanghai");

    userList.updateById(user2);
    userList.updateById(user);
}

```

##### 查询

```java
//批量查询
    @Test
    void selectBatchUsers(){
        List<Users> list = userList.selectBatchIds(Arrays.asList(1,2,3,19,20));
        list.forEach(System.out::println);
    }
```

##### 多条件查询

```java
 @Test
    void selectBatchUsers2(){
        Map<String,Object> map = new HashMap<>();
        map.put("name","chandler");
        map.put("orders_type",2);
        map.put("create_time",null);
        List<Users> users = userList.selectByMap(map);
        users.forEach(System.out::println);
    }
```

##### 分页查询

```java
//添加配置文件
@Component
@EnableTransactionManagement
public class MyBatisPlusConfig {
    //配置乐观锁插件
    @Bean
    public OptimisticLockerInterceptor optimisticLockerIntercepter(){
        return new OptimisticLockerInterceptor();
    }
    //配置分页插件
    @Bean
    public PaginationInterceptor paginationInnerInterceptor(){
        return new PaginationInterceptor();
    }
}
```

```java
//测试查询
@Test
    void getPagesSelect(){
        Page<Users> page = new Page<>(1,12);
        userList.selectPage(page,null);
        page.getRecords().forEach(System.out::println);
    }
```

```java
//条件删除
    @Test
    void deleteUsers(){
        Map<String,Object> map = new HashMap<>();
        map.put("name","wangnan");
        userList.deleteByMap(map);
    }
```

##### 逻辑删除

1. 添加字段 deleted
2. 添加注解

```java
 @TableLogic
    private Integer deleted;
```

3. 添加配置

```properties
mybatis-plus.global-config.db-config.logic-delete-value=0
mybatis-plus.global-config.db-config.logic-not-delete-value=1
```

##### Wrapper

```java
@Test
    void wrapperTest(){
        QueryWrapper<Users> wrapper = new QueryWrapper<>();
        wrapper.eq("name","chandler")
                .ge("id",10)
                .isNotNull("create_time");
        userList.selectList(wrapper).forEach(System.out::println);
    }
```

```java
//查询一个
QueryWrapper<Users> wrapper = new QueryWrapper<>();
        wrapper.eq("name","chandler")
                .ge("id",10)
                .isNotNull("create_time");
        Users user = userList.selectOne(wrapper);
```

