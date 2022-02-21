![image-20211121105258997](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211121105258997.png)

#### 常用的注解

> 1. <a>**@Data**</a> 
>
>    作用在类上，用来生成<a>getter,setter,toString,hashCode,equals</a> 等方法
>
> 2. <a>**@Getter,@Setter**</a>
>
>    作用在类上，用来生成<a>getter,setter</a> 
>
> 3. <a>**@ToString**</a> 
>
>    生成<a>toString</a> 
>
> 4. <a>**@AllArgsContructor，@NoArgsConstructor**</a> 
>
>    用在类上，提供全参构造和无参构造方法
>
> 5. <a>**@Accessors**</a> 
>
>    用在类上，用来给类中的setter方法开启链式调用<a>chain</a> 属性。
>
> 6. <a>**@Slf4j**</a> 
>
>    用在类上，快速给类中定义一个日志变量。
>
>    相当于在类中声明一个日志对象
>
>    ```java
>    private Logger log = LoggerFactory.getLogger(this.getClass());
>    ```
>
>    
>
> 7. 

```java
//实体类
@Data
@Accessors(chain=true)
public class User{
    private String id;
    private String name;
    private Integer age;
    private Date bir;
}
//演示调用
User user = new User();
user.setName("chandler").setId("12").setAge(20).setBir(new Date());//使用链式调用，避免了传统setter多行代码的冗余。
```

```java
//@Slf4j
@Controller
@Slf4j
public class UserContrller {
    @RequestMapping(value="/user")
    public @ResponseBody
    String slf(){
        log.info("酸辣粉4斤");
        log.info("进入{}方法","slf");//占位符{}
        return "slf";
    }
}


@Controller
@Slf4j
public class UserContrller {
    @Autowired
    private User user;

    @RequestMapping(value="/user")
    public @ResponseBody
    String slf(){
        user.setUserName("chandler").setId(22);
        log.info("姓名:{},ID:{}",user.getUserName(),user.getId());//避免了字符串拼接
        return "slf";
    }
}
```

