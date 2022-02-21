### 9.1 异步任务

> 根据异步处理方式的不同，可以将异步任务的调用分为无返回值的异步任务调用和由返回值的异步任务调用

#### 9.1.1 无返回值的异步任务调用

1. 

```java
@Service
public class MyAsyncTest {
    @Async
    public void sendMessage() throws InterruptedException {
        System.out.println("sendMessage调用...");
        long currentSecond = System.currentTimeMillis();
        Thread.sleep(5000);
        long lastSecond = System.currentTimeMillis();
        System.out.println("短信业务耗时"+(lastSecond-currentSecond));
    }
}
```

2. 开启基于注解的异步任务支持

```java
@EnableAsync    //开启核心引导类支持
@SpringBootApplication
public class ItheimaChapter09Application {

    public static void main(String[] args) {
        SpringApplication.run(ItheimaChapter09Application.class, args);
    }

}
```

3. 主程序

```java
@Controller
public class MyAsyncController {
    @Autowired
    private MyAsyncTest test;
    @RequestMapping(value="/async")
    public @ResponseBody String getSecond() throws InterruptedException {
        long currentSeconds = System.currentTimeMillis();
        test.sendMessage();
        long lastSeconds = System.currentTimeMillis();
        System.out.println("主程序耗时"+(lastSeconds-currentSeconds));
        return "response";
    }
}
```

运行结果

```pro
主程序耗时4
sendMessage调用...
短信业务耗时5010
```

> 上述案例中的异步方法是没有返回值的，这样主流程在执行异步方法时不会阻塞，而是继续向下执行主流程程序。调用的异步方法回作为一个子线程单独执行，知道异步方法执行完成。

#### 9.1.2 有返回值异步任务调用

1. 编写异步调用方法

```java
@Async
    public Future<Integer> processA() throws InterruptedException {
        System.out.println("分析并统计A业务...");
        long currentSeconds = System.currentTimeMillis();
        Thread.sleep(4000);
        long lastSeconds = System.currentTimeMillis();
        //模拟统计结果
        int counts = 12345;
        System.out.println("业务A用时 "+(lastSeconds - currentSeconds)+" ms");
        return new AsyncResult<Integer>(counts);
    }
    @Async
    public Future<Integer> processB() throws InterruptedException {
        System.out.println("分析并统计B业务...");
        long currentSeconds = System.currentTimeMillis();
        Thread.sleep(5000);
        long lastSeconds = System.currentTimeMillis();
        //模拟统计结果
        int counts = 12345;
        System.out.println("业务B用时 "+(lastSeconds - currentSeconds)+" ms");
        return new AsyncResult<Integer>(counts);
    }
```

2. 开启核心引导类支持

3. 控制层

```java
  @RequestMapping(value="/count")
    public @ResponseBody String getRes() throws InterruptedException, ExecutionException {
        System.out.println("主程序启动...");
        long currentTime = System.currentTimeMillis();
        Future<Integer> futureA = test.processA();
        Future<Integer> futureB = test.processB();
        int total = futureA.get() + futureB.get();
        System.out.println("统计结果汇总"+total);
        long lastTime = System.currentTimeMillis();
        System.out.println("主程序运行结果"+(lastTime - currentTime));
        return "result";
    }
```

运行结果

```pro
主程序启动...
分析并统计A业务...
分析并统计B业务...
业务A用时 4012 ms
业务B用时 5011 ms
统计结果汇总24690
主程序运行结果5023
##在主流程打印输出结果之间一致等待业务A和业务B两个异步方法的异步调用和结果汇总。
```

> 上述异步方法是由返回值的，主程序在执行异步方法时会有短暂阻塞，需要等待并获取异步方法的返回结果，而调用的两个异步方法会作为两个子线程并行执行，直到异步方法执行完成并返回结果，这样主流程会在最后一个异步方法返回结果后跳出阻塞状态。

#### 9.2.1 定时任务

> Spring 框架的定时任务调度功能支持配置和注解两种方式，<a>Spring Boot</a> 在Spring的基础上实现了继承，并对其中地基与注解方式的定时任务实现了非常好的支持

> **<a>@EnableScheduling</a>**
>
> <a>@EnableScheduling</a> 注解是Spring提供的，用于开启基于注解方式的定时任务支持，主要用在**项目启动类**上。
>
> <a>**@Scheduled**</a>
>
> <a>@Scheduled</a> 注解是Spring提供的，配置定时任务的执行规则，该注解主要用于在定时业务方法上<a>@Scheduled</a> 提供了多个属性：
>
> | 属性                      | 说明                                                         |
> | ------------------------- | ------------------------------------------------------------ |
> | <a>cron</a>               | 类似于<a>cron</a> 的表达式，可以定制定时任务触发的秒、分钟、小时、月中的日、月、周中的日 |
> | <a>zone</a>               | 指定<a>cron</a> 表达式将被解析的时区。默认情况下，该属性为空字符串，即使用服务器的本地时区 |
> | <a>fixedDelay</a>         | 表示在上一次任务执行结束后在指定时间后继续执行下一次任务(属性值为long类型) |
> | <a>fixedDelayString</a>   | 表示在上一次任务执行结束后在指定时间后继续执行下一次任务(属性值为long类型的字符串形式) |
> | <a>fixedRate</a>          | 表示每隔指定时间执行一次任务(属性值为long类型)               |
> | <a>fixedRateString</a>    | 表示每隔指定时间执行一次任务(属性值为long类型的字符串形式)   |
> | <a>initialDelay</a>       | 表示在<a>fixedRate</a> 或<a>fixedDelay</a> 任务第一次执行之前要延迟的毫秒数(属性值为long类型) |
> | <a>initialDelayString</a> | 表示在<a>fixedRate</a> 或<a>fixedDelay</a> 任务第一次执行之前要延迟的毫秒数(属性值为long类型的字符串形式) |
>
> 

#### 9.2.2 定时任务实现

1. 定时任务

```java
package com.szjm.itheimachapter09.service;

import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Service;

import java.text.SimpleDateFormat;
import java.util.Date;

/**
 * @author chixinyu
 * @version 2021/11/24 9:35
 */
@Service
public class SchedualTaskService {
    private static final SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
    private Integer count1 =1;
    private Integer count2 =1;
    private Integer count3 =1;
    @Scheduled(fixedRate = 6000)
    public void scheduledTaskImmediately(){
        System.out.println(String.format("fixedRate第%s次执行，当前时间为%s",count1++,dateFormat.format(new Date())));
    }
    @Scheduled(fixedDelay = 6000)
    public void scheduledTaskAfterSleep() throws InterruptedException {
        System.out.println(String.format("fixedDelay第%s次执行，当前时间为%s",count2++,dateFormat.format(new Date())));
        Thread.sleep(10000);
    }

    @Scheduled(cron = "0 * * * * *")
    public void scheduledTaskCron(){
        System.out.println(String.format("cron第%s次执行，当前时间为%s",count3++,dateFormat.format(new Date())));
    }
}

```

2. 开启基于注解的定时任务支持

```java
@EnableScheduling
```

运行结果

![image-20211124093948116](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211124093948116.png)

> 从演示结果可以看出，项目启动后，配置<a>@Scheduled</a> 注解的<a>fixedRate</a> 和<a>fixedDelay</a>属性的定时方法会立即执行一次，配置<a>cron</a> 属性的定时方法会在整数分钟时间点首次执行。
>
> 配置<a>fixedRate</a> 和<a>cron</a> 属性的方法会每隔一分钟重复执行一次定时任务，而配置<a>fixedDelay</a> 属性的方法时在上一次方法执行完成后在相隔一分钟重复执行一次定时任务。

#### 9.3.1 发送纯文本邮件

1. 添加邮件服务依赖启动器

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-mail</artifactId>
</dependency>
<!--添加上述依赖后，Spring Boot自动配置的邮件服务会生效，在发送邮件任务时，可以直接使用Spring框架提供的JavaMailSender接口或它的实现类JavaMailSenderImpl邮件发送-->
```

2. 添加邮件服务配置

```properties
#发件人邮箱服务器相关配置
spring.mail.host=smtp.qq.com
spring.mail.port=587
#配置个人QQ账号和密码(密码是加密后的授权码)
spring.mail.username=2633401696@qq.com
spring.mail.password=qmgklwpsymcqebbi
spring.mail.default-encoding=UTF-8
#邮件服务超时时间设置
spring.mail.properties.mail.smtp.connectiontimeout=5000
spring.mail.properties.mail.smtp.timeout=3000
spring.mail.properties.mail.smtp.writertimeout=5000
```

3. 返送邮件service

```java
@Service
public class SendEmail {
    @Autowired
    private JavaMailSenderImpl javaMailSender;
    @Value("${spring.mail.username}")
    private String from;

    public void sendSimpleEmail(String to,String subject,String text){
        SimpleMailMessage simpleMailMessage = new SimpleMailMessage();
        simpleMailMessage.setFrom(from);
        simpleMailMessage.setTo(to);
        simpleMailMessage.setText(text);
        simpleMailMessage.setSubject(subject);
        try{
            javaMailSender.send(simpleMailMessage);
            System.out.println("纯文本");
        }catch(MailException e){
            System.out.println("发送失败"+e.getMessage());
            e.printStackTrace();
        }
    }
}
```

4. 测试类

```java
@Autowired
    private SendEmail sendEmail;
    @Test
    void contextLoads() {
        String text = "hello world";
        String to = "chi_2021@163.com";
        String subject = "simple email test";
        sendEmail.sendSimpleEmail(to,subject,text);
    }
```

#### 9.3.2 发送带附件和图片的邮件

1. 定制邮件发送功能

```java
package com.szjm.itheimachapter09.service;

@Service
public class SendEmail {
    @Autowired
    private JavaMailSenderImpl javaMailSender;
    @Value("${spring.mail.username}")
    private String from;

    public void sendSimpleEmail(String to,String subject,String text){
        SimpleMailMessage simpleMailMessage = new SimpleMailMessage();
        simpleMailMessage.setFrom(from);
        simpleMailMessage.setTo(to);
        simpleMailMessage.setText(text);
        simpleMailMessage.setSubject(subject);
        try{
            javaMailSender.send(simpleMailMessage);
            System.out.println("纯文本");
        }catch(MailException e){
            System.out.println("发送失败"+e.getMessage());
            e.printStackTrace();
        }
    }
    public void sendComplexEmail(String to,String subject,String text,String filePath,String rscId,String rscPath){
        //定制复杂邮件信息MimeMessage
        MimeMessage message = javaMailSender.createMimeMessage();
        try{
            //使用MimeMessageHelper帮助类，并设置multipart多部件使用为true
            MimeMessageHelper helper = new MimeMessageHelper(message,true);
            helper.setFrom(from);
            helper.setTo(to);
            helper.setSubject(subject);
            helper.setText(text,true);
            //设置邮箱静态资源
            FileSystemResource res = new FileSystemResource(new File(rscPath));
            helper.addInline(rscId,res);
            //设置邮件附件
            FileSystemResource file = new FileSystemResource(new File(filePath));
            String fileName = filePath.substring(filePath.lastIndexOf(File.separator));
            helper.addAttachment(fileName,file);
            javaMailSender.send(message);
            System.out.println("complex email发送成功");

        } catch (MessagingException e) {
            System.out.println("complex email发送失败"+e.getMessage());
            e.printStackTrace();
        }
    }
}

```

> 在定制复杂邮件信息时使用了<a>MimeMessageHelper</a> 类对邮件信息封装处理，包括设置内嵌静态资源和邮件附件。其中设置邮件内嵌静态资源的方法为<a>addInline(String contentId,Resource resource)</a> ,设置邮件附件的方法为<a>addAttachment(String attachmentFileName,InputStreamSource inputStreamSource)</a> 

2.  测试

```java
@Test
    void complexEmailTest(){
        String to = "chi_2021@163.com";
        String subject = "complex email test";
        StringBuilder builder = new StringBuilder();
        builder.append("<html><head></head><body>");
        builder.append("<h1>complex email test</h1>");
        //cid 为固定写法，rscid为自定义资源的唯一标识
        String rscId = "1";
        builder.append("<img src='cid:"+rscId+"'/></body>");
        builder.append("</html>");
        String rscPath = "E:\\桌面\\补充案例\\chapter06\\images\\1.jpg";
        String textPath = "E:\\桌面\\补充案例\\寒假作业催交.txt";
        sendEmail.sendComplexEmail(to,subject,builder.toString(),textPath,rscId,rscPath);
    }
```

#### 9.3.3 发送邮件模板

1. 添加<a>Thymeleaf</a> 模板引擎依赖启动器

2. 定制模板邮件

```html
<body>
    <h2 th:text="${username}"></h2><span> ，你好</span>
    <p>你的验证码为<span th:text="${code}"></span> ,请妥善保管</p>
</body>
```

3. 

```java
public void sendTemplateEmail(String to,String subject,String content){
        //定制复杂邮件信息MimeMessage
        MimeMessage message = javaMailSender.createMimeMessage();
        try{
            //使用MimeMessageHelper帮助类，并设置multipart多部件使用为true
            MimeMessageHelper helper = new MimeMessageHelper(message,true);
            helper.setFrom(from);
            helper.setTo(to);
            helper.setSubject(subject);
            helper.setText(content,true);
            javaMailSender.send(message);
            System.out.println("complex email发送成功");

        } catch (MessagingException e) {
            System.out.println("complex email发送失败"+e.getMessage());
            e.printStackTrace();
        }
    }
```

4. 测试

```java
 @Autowired
    private TemplateEngine templateEngine;
    @Test
    void templateTest(){
        String to = "chi_2021@163.com";
        String subject = "template email test";
        //使用模板页面定制正文内容
        Context context = new Context();
        context.setVariable("username","chandler");
        context.setVariable("code","123456");
        //使用TemplateEngine设置要处理的模板页面
        String content = templateEngine.process("sendEmail",context);
        sendEmail.sendTemplateEmail(to,subject,content);

    }
```

