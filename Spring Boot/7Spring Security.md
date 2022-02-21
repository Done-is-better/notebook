### 7.1 Spring Security介绍

> <a>Spring Security</a> 是一个基于Spring生态圈，用于提供安全访问控制解决方案的框架。<a>Spring Boot</a> 对<a>Spring Security</a> 提供了整合支持，并提供了通用的自动化配置，从而实现了<a>Spring Security</a> 安全框架中包含的多数安全管理功能。

```xml
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>
```

### 7.3 <a>MVC Security</a> 安全配置

> 使用<a>Spring Boot</a> 与<a>Spring MVC</a> 进行web开发时，如果项目引入<a>spring-boot-starter-security</a> 依赖启动器，<a>MVC Security</a> 安全管理功能就会自动生效，其默认的安全配置是在<a>SecurityAutoConfiguration</a> 会导入并自动化配置<a>SpringBootWebSecurityConfiguration</a> 用于启动web 安全管理，<a>UserDetailsServiceAutoConfiguration</a> 则用于配置用户身份信息。
>
> 要完全关闭<a>Security</a> 提供的 web应用默认安全配置，可以自定义<a>WebSecurityConfigurerAdapter</a> 类型的Bean组件。但是不会关闭<a>UserDetailsService</a> 用户信息自动配置类。如果要关闭<a>UserDetailsService</a> 默认的用户信息配置，可以自定义<a>UserDetailsService、AuthenticationProvider或 AuthenticationManager</a> 类型的Bean组件。另外，可以通过自定义<a>WebSecurityConfigurerAdapter</a> 类型的Bean组件来覆盖默认访问规则。<a>Spring Boot</a> 提供了多种方法，可用于覆盖请求映射和静态资源的访问规则。
>
> **<a>WebSecurityConfigurerAdapter</a>类的主要方法**
>
> | 方法                                                | 描述                             |
> | --------------------------------------------------- | -------------------------------- |
> | <a>configure(AuthenticationManagerBuilder auth)</a> | 定制用户认证管理器来实现用户认证 |
> | <a>configure(HttpSecurity http)</a>                 | 定制基于HTTP请求的用户访问控制   |
>
>  

### 7.4 自定义用户认证

> 通过自定义<a>WebSecurityConfigurerAdapter</a> 类型的Bean组件，并重写<a>configure(AuthenticationManagerBuilder auth)</a> 方法可以自定义用户认证。针对自定义用户认证，<a>Spring Security</a> 提供了多种自定义认证方式，包括有<a>In-Memory Authentication(内存身份认证)、JDBC Authentication(JDBC身份认证)、LDAP Authentication(LDAP身份认证)、AuthenticationProvider(身份认证提供商)和UserDetailsService(身份详情服务)</a> .

#### 7.4.1 内存身份认证

> <a>In-Memory Authentication(内存身份认证)</a> 是最简单的身份认证方式，主要用于<a>Security</a> 安全认证体验和测试。
>
> 自定义身份认证时，只需要在重写的<a>configure(AuthenticationManagerBuilder auth)</a> 方法中定义测试用户即可。

1. 自定义<a>WebSecurityConfigurerAdapter</a> 配置类

```java
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    protected void configure(AuthenticationManagerBuilder auth)throws Exception{
        //设置密码编码器
        BCryptPasswordEncoder b = new BCryptPasswordEncoder();
        //模拟测试用户
        InMemoryUserDetailsManagerConfigurer<AuthenticationManagerBuilder>
                a = auth.inMemoryAuthentication().passwordEncoder(b);
        a.withUser("shitou").password(b.encode("123")).roles("comment");
        InMemoryUserDetailsManagerConfigurer<AuthenticationManagerBuilder> au = auth.inMemoryAuthentication().passwordEncoder(b);
        au.withUser("chandler").password(b.encode("1234")).roles("VIP");
    }
}
```



> <a>**@EnableWebSecurity**</a> 注解是一个组合注解，主要包括：
>
> <a>*@Configuration*</a> :作用是将当前自定义的<a>SecurityConfig</a> 类作为<a>Spring Boot</a> 的配置类
>
> <a>*@Import({WebSecurityConfiguration.class,Spring WebMvcImportSelector.class})*</a> :作用是根据<a>pom.xml</a>中导入的Web 模板和Security模块进行自动化配置
>
> <a>*@EnableGoobalAuthentication*</a> ：用于开启自定义的全局认证。

#### 7.4.2 <a>JDBC</a>身份认证

> <a>JDBC Authentication(JDBC身份认证)</a> 是通过<a>JDBC</a> 连接数据库进行已有用户身份认证，避免了内存身份认证的弊端，可以实现对已注册用户的身份认证。

1. 添加依赖

```xml
 <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
        </dependency>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
```

```java
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Qualifier("dataSource")
    @Autowired
    private DataSource dataSource;
    protected void configure(AuthenticationManagerBuilder auth)throws Exception{
        //设置密码编码器
        BCryptPasswordEncoder b = new BCryptPasswordEncoder();

                //使用JDBC进行身份认证
                JdbcUserDetailsManagerConfigurer<AuthenticationManagerBuilder> a = auth.jdbcAuthentication().passwordEncoder(b);
                a.usersByUsernameQuery("select username,password,valid from t_customer where username =?");
                a.authoritiesByUsernameQuery("select c.username,a.authority from t_customer c,t_authority a,t_customer_authority ta where c.id=ta.customer_id and a.id = ta.authority_id and c.username = ?");


```



#### 7.4.3 <a>UserDetailsService</a>身份认证

> 对于用户流量较大的项目，频繁的使用<a>JDBC</a> 进行数据库查询，会降低网站登录访问速度。

...



#### 7.4.4 自定义用户访问控制

通过重写<a>WebSecurityConfigurerAdapter</a> 类的<a>configure(HttpSecurity http)</a> 方法可以对基于HTTP的请求访问进行控制。

> <a>HttpSecurity</a> 类的主要方法

| 方法                       | 描述                                             |
| -------------------------- | ------------------------------------------------ |
| <a>authorizeRequests()</a> | 开启基于<a>HttpSercletRequest</a> 请求访问的限制 |
| <a>formLogin()</a>         | 开启基于表单的用户登录                           |
| <a>httpBasic()</a>         | 开启基于HTTP请求的<a>Basic</a> 认证登录          |
| <a>logout()</a>            | 开启退出登录的支持                               |
| <a>sessionManagement</a>   | 开启Session管理配置                              |
| <a>rememberMe()</a>        | 开启记住我功能                                   |
| <a>csrf()</a>              | 配置<a>CSRF</a> 跨站请求伪造防护功能             |

> <a>authorizeRequests()</a> 方法返回值

| 方法                                                    | 描述                                 |
| ------------------------------------------------------- | ------------------------------------ |
| <a>antMatchers(java.lang.String... antPatterns)</a>     | 开启Ant风格的路径匹配                |
| <a>mvcMatchers(java.lang.String ...patterns)</a>        | 开启MVC 风格的路径匹配               |
| <a>regexMatchers(java.lang.String ...regexPatterns)</a> | 开启正则表达式的路径匹配             |
| <a>and()</a>                                            | 功能连接符                           |
| <a>anyRequest()</a>                                     | 匹配任何请求                         |
| <a>rememberMe()</a>                                     | 开启记住我功能                       |
| <a>access(String attribute)</a>                         | 使用基于SpEL表达式的角色现象匹配     |
| <a>hasAnyRole(String ...roles)</a>                      | 匹配用户是否有参数中的任意角色       |
| <a>hasRole(String role)</a>                             | 匹配用户是否有某一个角色             |
| <a>hasAnyAuthority(String ...authorities)</a>           | 匹配用户是否有参数中的任意权限       |
| <a>hasAuthority(String authority)</a>                   | 匹配用户是否有某一个权限             |
| <a>authrnticated()</a>                                  | 匹配已经登录认证的用户               |
| <a>fullyAuthenticated()</a>                             | 匹配完整登录认证的用户，非rememberMe |
| <a>hasIpAddress(String ipaddressExpression)</a>         | 匹配某IP地址的访问请求               |
| <a>permitAll()</a>                                      | 无条件对请求进行放行                 |



#### 7.4.5 自定义用户登录

###### 用户登录相关方法

| 方法                                                         | 描述                                            |
| ------------------------------------------------------------ | ----------------------------------------------- |
| <a>loginPage(String loginPage)</a>                           | 用户登录页面跳转类路径，默认为get请求的"/login" |
| <a>successForwardUrl(String forwardUrl)</a>                  | 用户登录成功后的重定向地址                      |
| <a>successHandler(AuthenticationSuccessHandler successHandler)</a> | 用户登录成功后的处理                            |
| <a>defaultSuccessUrl(String defaultSuccessUrl)</a>           | 用户直接登录后默认跳转地址                      |
| <a>failureForwardUrl(String forwardUrl)</a>                  | 用户登陆失败后的重定向地址                      |
| <a>failureUrl(String authrenticationFailureUrl)</a>          | 用户登录失败后的跳转地址，默认为 "/login?error" |
| <a>failureHandler(AuthenticationFailureHandler authenticationFailureHandler)</a> | 用户登录失败后的错误处理                        |
| <a>usernameParameter(String usernameParameter)</a>           | 登录用户的用户名参数，默认为username            |
| <a>passwordParameter(String passwordParameter)</a>           | 登录用户的密码参数，默认为password              |
| <a>loginProcessingUrl(String loginProcessingUrl)</a>         | 登录表单提交的路径，默认为post请求的"/login"    |
| <a>permitAll()</a>                                           | 无条件对请求进行放行                            |

1. 定义视图

```html
<form th:action="@{/userlogin}" th:method="post">
      <h1>请登录</h1>
      <div th:if="${param.error}">用户名或密码错误，请重新登陆</div>
      <input type="text" name="name" placeholder="用户名"/>
      <input type="password" name="password" placeholder="密码"/>
      <div>
        <label><input type="checkbox" name="rememberme"/>记住我</label>
      </div>
      <button type="submit">登录</button>
      <p>@Copyright 2019-2022</p>
    </form>
```

2. 自定义用户登录跳转

```java
//Spring Security默认使用get方式的"/login"请求用于向登录页面跳转，默认使用post方式"/login"请求用于对登陆后的数据进行处理
@RequestMapping(value="/userlogin")
    public String toLoginPage(){
        return "login";
    }
```

3. 自定义用户登录控制

```java
protected void configure(HttpSecurity http) throws Exception{
    http.authorizeRequests().antMatchers("/").permitAll()//对"/"路径放行
        .antMatchers("/**").permitAll()
        .antMatchers("/details/common/**").hasRole("common")//对"/details/common/"路径的请求，需要common权限，即ROLE_common
        .antMatchers("/details/vip/**").hasRole("vip")
        .anyRequest().autenticated() //未映射的请求必须登录认证
        .and().formLogin();	//配置类Security默认登录页面
    //自定义用户登录控制
    http.formLogin().loginPage("/userLogin").permitAll()
        .usernameParameter("name")
        .passwordParameter("password")
        .defaultSuccessUrl("/")
        .failureUrl("/userLogin?error");
}
```

#### 7.5.3 自定义用户退出

自定义用户退出主要考虑退出后会话如何管理以及跳转到那个页面，<a>HttpSecurity</a> 类的logout()方法用来处理用户退出，默认的处理路径为<a>"/logout"</a> 的post请求，同时会清除Session和 Rememberme等任何默认用户配置。

用户退出涉及到用户推出的主要方法

| 方法                                                         | 描述                                                        |
| ------------------------------------------------------------ | ----------------------------------------------------------- |
| <a>logoutUrl(String logoutUrl)</a>                           | 用户退出吃力控制URL，默认为post请求的/logout                |
| <a>logoutSuccessUrl(String logoutSuccessUrl)</a>             | 用户退出成功后的重定向地址                                  |
| <a>logoutSuccessHandler(LogoutSuccessHandler logoutSuccessHandler)</a> | 用户退出成功后的处理器设置                                  |
| <a>deleteCookies(String ...cookieNamesToClear)</a>           | 用户退出后删除指定cookie                                    |
| <a>invalidateHttpSession(boolean invalidateHttpSession)</a>  | 用户退出后是否立即清除session(默认为true)                   |
| <a>clearAuthentication(boolean clearAuthenticatioin)</a>     | 用户退出后是否立即清除authentication用户认证信息,默认为true |
|                                                              |                                                             |

> **注意**
>
> <a>Spring Boot</a> 项目中引入<a>Spring Security</a> 框架后会自动 开启<a>CSRF</a> 防护功能（请求跨站伪造防护），用户退出是必须使用post请求。如果关闭了<a>CSRF</a>  防护功能，可以使用任意方式的HTTP请求进行用户注销。

```
<form th:action="@{/logout}" method="post">
    <button type="submit">退出</button>
</form>
```

```java
http.logout().logoutUrl("/logout").logoutSuccessUrl("/logoutSuccess");
```



#### 7.5.4 登录用户信息获取

在传统项目中进行用户登录时，通常会查询用户是否存在，如果存在就登录成功，同时将当前用户放在Session中。



1. <span style="color:red">使用HttpSession获取用户信息</span>

```java
@GetMapping("/")
@ResponseBody
public void getUser(HttpSession session){
    //从当前HttpSession获取绑定到词汇化的所有对象的名称
    Enumeration<String> names = session.getAttributeNames();
    while(names.hasMoreElements()){
        //获取HttpSession中会话名称
        String element = names.nextElement();
        //获取HttpSession中的应用上下文
        SecurityContextImpl attribute = (SecurityContextImpl) session.getAttribute(element);
        //获取用户相关信息
        Authentication authentication = attribute.getAuthentication();
        UserDetails principal = (UserDetails) authentication.getPrincipal();
        
    }
}
```



2. <span style="color:red">使用SecurityContextHolder获取用户信息</span> 

```java
@RequestMapping("/getUser")
public @ResponseBody getUser(){
    SecurityContext context = SecurityContextHolder.getContext();
    Authentication authentication  = context.getAuthentication();
    UserDetails principal = (UserDetails) authentication.getPrincipal();
    System.out.println(principal.getUsername());
}
```



#### 7.5.5 RememberMe 

rememberme

| 方法                                                         | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <a>rememberMeParameter(String rememberMeParameter)</a>       | 指定在登录时记住用户的HTTP参数                               |
| <a>key(String key)</a>                                       | 记住我认证生成的Token令牌标识                                |
| <a>tokenValiditySeconds(int tokenValiditySeconds)</a>        | 记住我Token令牌有效期，单位s                                 |
| <a>tokenRepository(PersistentTokenRepository tokenRepository)</a> | 指定要使用的PersistentTokenRepository,用来匹配持久化Token令牌 |
| <a>alwaysRemember(boolean alwaysRemember)</a>                | 是否应该始终创建记住我cookie，默认为false                    |
| <a>clearAuthentication(boolean clearAuthentication)</a>      | 是否设置cookie为安全的，如果设置为true，则必须通过HTTPS进行连接请求。 |

> <a>Spring Security</a> 针对Remember-me 提供了两种实现：一种是简单的使用加密来保证基于cookie中Token的安全；另一种是通过数据或其他持久化机制来保存生成的Token

一。<a>基于简单加密的Token的方式</a> 

> 基于简单加密的Token方式，当用户选择Remember-me并成功登陆后，Spring Security 会生成一个cookie并发送给客户端浏览器。其中，cookie值由以下方式组合加密而成：
>
> ```java
> base64(username+":"+expirationTime+":"+md5Hex(username + ":"+expirationTime+":"+password +":"+key))
> //expirationTime表示rememberMe中Token的实效日期，以毫秒为单位，key 表示防止修改Token地标识。
> ```
>
> 基于简单加密的Token的方式中的Token在指定时间内有效，且必须保证Token中包含的username，password和key没有被改变。但这种加密方式是存在隐患的，任何人获取到该RememberMe功能的Token后，都可以在该Token过期之前自动登录，只有当前用户察觉Token被盗后，才会对自己登录密码进行修改来立即使原有的Token实效。

```java
package com.szjm.itheimachapter07.config;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.authentication.configurers.provisioning.InMemoryUserDetailsManagerConfigurer;
import org.springframework.security.config.annotation.authentication.configurers.provisioning.JdbcUserDetailsManagerConfigurer;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;

import javax.sql.DataSource;

/**
 * @author chixinyu
 * @version 2021/11/20 19:45
 */

@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Qualifier("dataSource")
    @Autowired
    private DataSource dataSource;
protected void configure(HttpSecurity http) throws Exception{
    http.authorizeRequests().antMatchers("/").permitAll()
            .antMatchers("/**").permitAll()
            .antMatchers("/details/common/**").hasRole("common")
            .antMatchers("/details/vip/**").hasRole("vip")
            .anyRequest().authenticated()
            .and().formLogin();
    //自定义用户登录控制
    http.formLogin().loginPage("/userlogin").permitAll()
            .usernameParameter("name")
            .passwordParameter("password")
            .defaultSuccessUrl("/")
            .failureUrl("/userlogin?error");
    http.logout().logoutUrl("/logout").logoutSuccessUrl("/logoutSuccess");
    //rememberme
    http.rememberMe().rememberMeParameter("rememberme").tokenValiditySeconds(200);
}
}

```



二。<a>基于持久化Token的方式</a>

> 持久化Token的方式与简单加密Token的方式在实现Remember-Me功能上大体相同，都是在用户选择“记住我”功能并成功登陆后，将生成的Token存入cookie中并发送到客户端浏览器，在下次用户通过统一客户端访问系统时，系统将直接从客户端cookie中读取Token进行认证。两者的主要区别在于：基于简单加密Token的方式，生成的Token将在客户端保存一段时间，如果用户不退出登录，或不修改密码，在cookie失效之前，任何人都可以使用该cookie进行登录；二基于持久化Token的方式：
>
> 1. 用户选择“记住我”成功登陆后，Security会把username、随机产生的序列号、生成的Token进行持久化存储，同时将他们组合生成一个cookie发送给客户端浏览器。
> 2. 当用户再次访问系统时，首先检查客户端携带的cookie，如果对应cookie中包含的username、序列号和Token与数据库保存的一致，则通过验证并自动登录，同时系统将重新生成一个新的Token替换数据库中旧的Token，并将新的cookie再次发送给客户端。
> 3. 如果cookie中的Token不匹配，则可能是用户的cookie被盗用了。由于盗用者使用初次生成的Token进行登录会生成一个新的Token和cookie。同时Spring Security 可以发现cookie可能被到用的情况，它将删除数据库中与当前用户相关的所有Token记录 ，这样盗用者使用原有的cookie将不能再次登录。
> 4. 如果用户访问系统是没有携带cookie，或者包含的username和序列号与数据库中保存的不一致，那么将会引导用户到登录页面。

```java
package com.szjm.itheimachapter07.config;


@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Qualifier("dataSource")
    @Autowired
    private DataSource dataSource;
  
protected void configure(HttpSecurity http) throws Exception{
    http.authorizeRequests().antMatchers("/").permitAll()
            .antMatchers("/**").permitAll()
            .antMatchers("/details/common/**").hasRole("common")
            .antMatchers("/details/vip/**").hasRole("vip")
            .anyRequest().authenticated()
            .and().formLogin();
    //自定义用户登录控制
    http.formLogin().loginPage("/userlogin").permitAll()
            .usernameParameter("name")
            .passwordParameter("password")
            .defaultSuccessUrl("/")
            .failureUrl("/userlogin?error");
    http.logout().logoutUrl("/logout").logoutSuccessUrl("/logoutSuccess");
    //rememberme
    http.rememberMe().rememberMeParameter("rememberme")
            .tokenValiditySeconds(200)
            .tokenRepository(tokenRepository());
}
    @Bean
    public JdbcTokenRepositoryImpl tokenRepository(){
        JdbcTokenRepositoryImpl jdbcTokenRepository = new JdbcTokenRepositoryImpl();
        jdbcTokenRepository.setDataSource(dataSource);
        return jdbcTokenRepository;
    }
}

```



#### 7.5.6 CSRF 防护功能

> <a>CSRF(Cross-site request forgery)</a> 跨站请求伪造，也称为<a>One Click Attack</a> 或<a>Session Riding(会话控制)</a> 通常缩写成<a>CSRF</a>  或 <a>XSRF</a> ，是一种对网站的恶意利用。与传统的<a>XSS</a> 攻击<a>(Cross-site-Scripting)</a> 相比，CSRF攻击更难防范，被认为是比XSS更具危险性。
>
> CSRF攻击可以在受害者毫不知情的情况下以受害者的名义伪造请求发送攻击页面，从而在用户未授权的情况下执行在权限保护下的操作。
>
> 针对CSRF攻击要保护的对象是那些可以直接产生数据变化的服务，而对于读取数据的服务，可以不进行CSRF保护。
>
> 目前防御CSRF攻击主要由三种策略：
>
> 1.  验证HTTP Referer字段
> 2. 在请求地址中添加Token并验证
> 3. 在HTTP头中自定义属性并验证。

CSRF防御相关方法

| 方法                                                         | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <a>disable()</a>                                             | 关闭Security默认开启的CSRF防御功能                           |
| <a>csrfTokenRepository(CsrfTokenRepositor csrfTokenRepository)</a> | 指定要关闭的CsrfTokenRepository(Token令牌持久化仓库)。默认是由LazyCsrfTokenRepository包装的HttpSessionScrfTokenRepository |
| <a>requireCsrfProtectionMatcher(RequestMatcher requireCsrfProtectionMatcher)</a> | 指定针对什么类型的请求应用SCRF防护功能。默认设置是忽略<a>GET,HEAD,TRACE,OPTIONS</a> 请求，而处理并防御其他所有请求 |

> Spring Boot 整合 Spring Security默认开启了CSRF防御功能，并要求数据修改的请求方法都需要经过Security配置的安全认证后方可正常访问，否则无法正常发送请求。

> 关闭Spring Security 默认开启的CSRF防御功能
>
> ```java
> http.csrf().disable();
> ```
>
> 

2. <a>针对Form表单数据修改CSRF Token配置</a> 

```html
//Security 支持在Form表单中提交一个携带CSRF Token信息的隐藏域，与其他表单一起提交，这样后台就可以获取并验证该请求是否是安全的。
<form th:action="@{/updateUser}" method="post">
      <input type="hidden" th:name="${_csrf.parameterName}" th:value="${_csrf.token}"/>  
      用户名：<input type="text" name="username"/>
      密码:<input type="password" name="password"/>
      <input type="submit" value="提交"/>
  </form>
<!--Form表单中<input>隐藏标签携带了Security提供的CSRF Token信息。其中，th:name="${_csrf.parameterName}"会获取Security默认提供的CSRF Token对应的key值_scrf,th:value="${_csrf.token}"会获取Security默认随机生成的CSRF Token对应的value值。在Form表单中添加上面配置后，无需其他配置就可以正常实现数据修改请求，后台配置的Security会自动欧冠获取并识别请求中的CSRF Token信息并进行用户信息验证。-->
<!--除此之外，还可以使用Thymeleaf模板的th:action属性配置CSRF Token信息-->
<form th:action="@{/updateUser}" method="post"> 
      用户名：<input type="text" name="username"/>
      密码:<input type="password" name="password"/>
      <input type="submit" value="提交"/>
  </form>
<!--使用thymeleaf模板的th:action属性配置请求时，会默认携带CSRF Token信息，无需开发者手动添加-->
```

```java
@RequestMapping(value="/updateUser")
    public @ResponseBody String update(@RequestParam String username, @RequestParam String password,
                                       @RequestParam(required=false)HttpServletRequest request){//@RequsetParam默认required是false ，即必传的
        System.out.println("username"+username);
        System.out.println("password"+password);
        return "update";
    }
```

2. <a>针对Ajax数据修改请求的CSRF Token配置</a> 

对于Ajax类型的数据修改请求来说，Security提供了通过添加Http header 头信息的方式携带CSRF Token 信息进行请求验证。

```html
<!--首先在页面<head>标签中添加<meta>字标签，并配置CSRF Token信息-->
<head>
    <!--获取CSRF Token-->
    <meta name="_csrf" th:content="${_csrf.token}"/>
    <!--获取CSRF 头，默认为X-CSRF-TOKEN-->
    <meta name="_csrf_header" th:content="_csrf.headerName"/>
</head>
<!--上述代码中，在<head>标签中添加了两个<meta>字标签，分别来设置CSRF Token信息的属性头和具体生成的Security Token值信息。其中，在HTTP header头信息中携带的CSRF请求头header参数的默认值为X-CSRF-TOKEN,而请求头CSRF header对应的CSRF Token值也是随机生成的。-->
```

```html
<!--然后，在具体的Ajax请求中获取<meta>字标签中设置的CSRF Token信息并绑定在HTTP请求头中进行请求验证-->
$(function(){
	<!--获取<meta>标签中封装的CSRF Token信息-->
	var token = $("meta[name='_csrf']").attr("content");
	var header = $("meta[name='_csrf_header']").attr("content");
	<!--将头中的CSRF Token信息进行发送-->
	$(document).ajaxSend(function(e,xhr,options){
		xhr.setRequestHeader(header,token);
});
});
```



#### 7.6 Security管理前端页面

1. 添加依赖

```xml
<dependency>
	<groupId>org.thymeleaf.extras</groupId>
    <artifactId>thymeleaf-extras-springsecurity5</artifactId>
</dependency>
```

2. 使用<a>Security</a> 相关标签进行页面控制

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org" xmlns:sec="http://www.w3.org/1999/xhtml">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<div sec:authorize="isAnonymous()">
  <h2>
    请登录
  </h2>
</div>
<div sec:authorize="isAuthenticated()">
  <span sec:authentication="name" ></span>,您好 您的权限为  <span sec:authentication="principal.authorities"></span>
  <form th:action="@{/mylogout}" method="post">
    <input th:type="submit" th:value="注销"/>
  </form>
</div>
<hr/>
<div sec:authorize="hasRole('common')">
  <h3>
    普通电影
  </h3>
  <ul>
    <li><a th:href="@{/detail/common/1}">飞驰人生</a></li>
    <li><a th:href="@{/detail/common/2}">夏洛特烦恼</a></li>
  </ul>
</div>
<div sec:authorize="hasAuthority('Role_vip')">
  <h3>
    Vip专享
  </h3>
  <ul>
    <li><a th:href="@{/detail/vip/1}">速度与激情</a></li>
    <li><a th:href="@{/detail/vip/2}">猩球崛起</a></li>
  </ul>
</div>
</body>
</html>
```

