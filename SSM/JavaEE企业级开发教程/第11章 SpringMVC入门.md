### 11.1 Spring MVC 概述

> **Spring MVC 的特点**
>
> - 是Spring框架的一部分，方便利用Spring提供的其他功能
> - 灵活性强，易于与其他框架集成
> - 提供了一个前端控制器DispatcherServlet,使开发人员无需额外开发控制器对象。
> - 可自动绑定用户输入，并能正确的装换数据类型。
> - 内置了常见的校验器，校验用户输入。如果校验不通过，会重定向到输入表单。
> - 支持国际化。根据用户区域显示多国语言。
> - 支持多种视图技术。如JSP,Velociy,FreeMarker等
> - 使用基于XML的配置文件，在编辑后，不需要重新编译应用程序。



### 11.2 第一个Spring MVC应用

```xml
<!--配置前端控制器-->
    <servlet>
        <servlet-name>DispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--初始化时加载配置文件-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc-config.xml</param-value>
        </init-param>
    </servlet>
    <servlet-mapping>
        <servlet-name>DispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
```

```java
public class FirstController implements Controller {
    @Override
    public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
        ModelAndView mav = new ModelAndView();
        mav.addObject("msg","这是我的第一个MVC层");
        //设计逻辑视图名
        mav.setViewName("/jsp/hello.html");
        return mav;
    }
}
```

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    ${msg}
</body>
</html>
```

```xml
 <!--配置处理器Handler，映射firstController-->
    <bean name="/firstController" class="com.szjm.springchapter11_enhanced.controller.FirstController"/>
    <!--处理器映射器，将处理器的name作为URL进行查找-->
    <bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping"/>
    <!--处理器适配器，配置对适配器中HandlerRequest()方法进行调用-->
    <bean class="org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter"/>
    <!--视图解析器-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver"/>
```



### 11.3 Spring MVC的工作流程

> 1. 用户通过浏览器向服务器发送请求，请求会被Spring MVC的前端控制器DispatcherServlet拦截。
> 2. DispatcherServlet拦截到请求后，会调用HandlerMapping处理器映射器。
> 3. 处理器映射器根据请求URL找到具体的处理器，生成处理器对象及处理器拦截器(如果有则生成)一并返回给DispatcherServlet.
> 4. DispatcherServlet会通过返回信息选择合适的HandlerAdapter(处理器适配器)
> 5. HandlerAdapter会调用并执行Handler(处理器)，这里的处理器指的是程序中编写的Controller类，也被称为后端控制器。
> 6. Controller执行完成后，会返回一个ModelAndView对象，该对象包含视图名或包含模型和视图名。
> 7. HandlerAdapter将ModelAndView对象返回给DispatcherServlet.
> 8. DispatcherServlet会根据ModelAndView对象选择一个合适的ViewReslover(视图解析器)
> 9. ViewReslover解析后，会向DispatcherServlet中返回具体的View（视图）
> 10. DispatcherServlet对View进行渲染(将模型数据填充到视图)
> 11. 视图渲染结果返回给客户端浏览器显示。