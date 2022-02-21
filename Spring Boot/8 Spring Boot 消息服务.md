#### 8.1.1 为什么使用消息服务

> 在多数应用，尤其是分布式系统中，消息服务是不可或缺的组成部分，它使用起来比较简单，同时解决了许多难题，如：异步处理，应用解耦，流量削峰，分布式事务管理等，使用消息服务可以实现一个<a>高性能、高可用、高扩展的系统</a>

1. 异步请求

<a>消息队列处理方式</a> 

先将注册信息写入数据库，然后在极短的时间内将注册信息写入消息队列后即可返回响应信息，此时前端业务不需要理会不相干的后台业务处理，发送邮件和短信的业务会自动读取消息队列中的相关消息进行后续业务处理。

2. 应用解耦

传统方式处理订单业务，用户下单后订单服务会直接调用库存服务接口进行库存更新，缺点是一旦库存系统出现异常，订单服务会失败导致订单丢失。

使用消息队列，订单服务的下订单消息会快速写入消息队列，库存服务会监听并读取到订单，从而修改数据库。相较传统方式，更高效、安全。

3. 流量削峰

秒杀活动是流量削峰的一种应用场景，由于服务处理资源有限，因此出现峰值很容易造成服务器宕机。为了解决这个问题，通常会采用消息队列缓冲顺势高峰流量，对请求进行分层过滤。

4. 分布式事务管理

针对分布式事务管理，目前较为可靠的处理方式是基于消息队列的二次提交，在失败的情况下进行多次尝试，或者基于队列数据进行回滚操作。因此，在分布式系统中加入消息服务是既能保证性能不变，又能保证业务一致的方案。



#### 8.2.1 <a>RabbitMQ </a>简介

> <a>RabbitMQ</a>是基于<a>AMQP</a> 协议的轻量级、可靠、可伸缩和可移植的消息代理,Spring 使用<a>RabbitMQ</a> 通过<a>AMQP</a> 协议进行通信，在<a>Spring Boot</a> 中对<a>RabbitMQ</a> 进行了集成管理。
>
> 在所有的消息服务中心，消息中间件都会作为一个第三方消息代理，接收发布者发布的消息，并推送给消息消费者。不同的消息中间件内部转换消息的细节不同。
>
> ![image-20211123090139887](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211123090139887.png)
>
> 1. 消息发布者(publisher) 向<a>RabbitMQ</a> 代理(Broker) 指定的虚拟主机服务器(Virtual Host) 发送消息；
> 2. 虚拟主机服务器内部的交换机(Exchange) 接收消息，并将消息传递并存储到与之绑定(Binging) 的消息队列中(Queue)
> 3. 消息消费者(Consumer)通过一定的网络连接与消息代理建立连接，同时为了简化开支，在连接内部使用了多路复用的信道(Channel)进行消息的最终消费。

#### 8.2.2 <a>RabbitMQ</a>工作模式

<a>RabbitMQ</a> 消息中间件针对不同的服务需求，提供了多种工作模式。

> 1. <a>Work queue(工作队列模式)</a> 
>
>    ![image-20211123091144712](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211123091144712.png)
>
>    在<a>work queues</a> 工作模式中，不需要设置交换器(<a>RabbitMQ</a> 会使用内部交换器进行消息转换)，需要指定唯一的消息队列进行消息传递，并且可以有多个消息消费者。在这种模式下，多个消息消费者通过轮询的凡是一次接收消息队列中存储的消息，一旦消息被某个消费者接收，消息队列会将消息移除，而接收并处理消息的消费者必须在消费完一条消息后在准备接收下一条消息。
>
>    <a>work queues</a> 工作模式适用于较为繁重，且可以进行拆分处理的业务
>
> 2. <a>Publich/Subscribe(发布订阅模式)</a> 
>
>    ![image-20211123091851949](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211123091851949.png)
>
>    在 <a>Publish/Subscribe</a> 工作模式中，必须先配置一个<a>fanout</a>类型的转换器，不需要指定对应的路由键<a>(Routing key)</a> ,同时会将消息路由到每一个消息队列上，然后每一个刁曦队列都可以对相同的消息进行接收存储，进而由各自消息队列关联的消费者进行消费。 
>
>    <a>Publish/Subscribe</a> 工作模式适用于进行相同业务功能处理的场合。
>
> 3. <a>Routing(路由模式)</a> 
>
>    ![image-20211123092408959](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211123092408959.png)
>
>    在<a>Routing</a> 工作模式中，必须先配置一个direct类型的交换器，并制定不同的路由键值(Routing key)将对应的消息从浇花器路由到不同的消息队列进行存储，由消费者进行各自消费。
>
>    <a>Routing</a> 工作模式适用于进行不同类型消费分类处理的场合。
>
> 4. <a>Topics(统配符模式)</a> k
>
>    ![image-20211123092736673](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211123092736673.png)
>
>    在<a>Topics</a> 工作模式中，必须先配置一个topic类型的交换器，并制定不同的路由键值(<a>Routing key</a> )将对应的消息从交换器路由到不同的消息队列进行存储，然后由消费者进行各自消费。
>
>    <a>Topics</a> 模式与<a>Routing</a> 模式的主要不同在于：<a>Topics</a> 模式设置的路由键是包含通配符的，其中，#匹配多个字符，*匹配一个字符，然后与其他字符一起使用“."进行连接，从而组成动态路由键，在发送消息时，可以根据需求设置不同的路由键从而将消息路由到不同的消息队列。
>
>    <a>Topics</a> 工作模式适用于根据不同需求动态传递处理业务的场合。
>
> 5. <a>RPC</a> 
>
>    ![image-20211123093418578](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211123093418578.png)
>
>    <a>RPC</a> 工作模式与<a>work queues</a> 工作模式流程相似，都不需要设置交换器，需要指定唯一的消息队列进行消息传递。<a>RPC</a> 模式与<a>work queues</a> 模式的主要不同在于：<a>RPC</a> 模式是一个回环结构，主要针对分布式加购的消息传递业务，客户端Client先发送消息到消息队列，远程服务端Server获取消息，然后在写入另一个消息队列，向原始客户端client响应消息处理结果。
>
>    <a>RPC</a> 供果模式适用于远程服务调用的业务处理场合。
>
> 6. <a>Headers</a> 
>
>    <a>Headers</a> 工作模式在<a>RabbitMQ</a> 所支持的工作模式中是较为少用的一种模式，器主要流程与<a>Routing</a> 工作模式雷士。<a>Headers</a> 工作模式在使用时，必须设置一个headers类型的交换器，而不需要设置路由键，取而代之的是Properties属性配置中的headers头信息中使用key/value的形式配置路由规则。

#### 8.3.1 <a>RabbitMQ</a> 环境搭建

> <a>RabbitMQ</a> 默认提供两个端口<a>5672</a> 和<a>15672</a> ，其中5672用作服务端口号，15672用作可视化管理端口号

```properties
#RabbitMQ消息中间件连接配置
spring.rabbitmq.host=localhost
spring.rabbitmq.port=5672
spring.rabbitmq.username=guest
spring.rabbitmq.password=guest
#配置RabbitMQ虚拟主机路径/，默认可以省略
spring.rabbitmq.virtual-host=/
```



#### 8.4.1 <a>Publish/Subscribe(发布订阅模式)</a> 

> <a>Spring Boot</a> 整合<a>RabbitMQ</a> 中间件实现消息服务，主要围绕三个部分展开：定制中间件，消息发送者发送消息，消息消费者接收消息。

1. 基于<a>API</a>的方式实现<a>Publish/Subscribe</a> 

> 基于<a>API</a> 的方式使用Spring 提供的<a>API</a> 管理类<a>AmqpAdmin</a> 定制消息发送组件，并进行消息发送。
>
> 这种定制消息发送组件的方式与在<a>RabbitMQ</a> 可视化界面上通过对应面板进行组件操作的实现基本一样，都是通过管理员的身份，预先手动声明交换器、队列、路由键等，然后进行消息队列组装，
>
> 来共应用程序调用，从而实现消息服务。

- 使用<a>AmqpAdmin</a> 定制消息发送组件

```java
package com.szjm.itheimachapter08;

import org.junit.jupiter.api.Test;
import org.springframework.amqp.core.AmqpAdmin;

import org.springframework.amqp.core.Binding;
import org.springframework.amqp.core.FanoutExchange;
import org.springframework.amqp.core.Queue;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class ItheimaChapter08ApplicationTests {

    //启动程序
    @Autowired
    private AmqpAdmin amqpAdmin;//使用Spring框架提供的消息管理组件AmqpAdmin定制消息组件
    @Test
    public void amqpAdmin(){
        //定义fanout类型的交换器
        amqpAdmin.declareExchange(new FanoutExchange("fanout_exchange"));
        //定义两个默认持久化队列，分别是email和sms
        amqpAdmin.declareQueue(new Queue("fanout_queue_email"));
        amqpAdmin.declareQueue(new Queue("fanout_queue_sms"));
        //将队列分别与交换器进行绑定
        amqpAdmin.declareBinding(new Binding("fanout_queue_email", Binding.DestinationType.QUEUE,"fanout_exchange","",null));
        amqpAdmin.declareBinding(new Binding("fanout_queue_sms",Binding.DestinationType.QUEUE,"fanout_exchange","",null));
    }
    @Test
    void contextLoads() {
    }

}

```

- 消息发送者发送消息

```java
@Autowired
    private User user;
    @Autowired
    private RabbitTemplate rabbitTemplate;
    @Test
    void contextLoads() {
        user.setUsername("chandler").setPassword("123");
        rabbitTemplate.convertAndSend("fanout_exchange","",user);
    }
//使用RabbitTemplate组件模板工具类的convertAndSend(String exchange,String routingKey,Object object)方法将消息发布。其中，第一个参数表示放松消息的交换器，参数值要与定制的交换器名称一致，第二个参数表示路由键，因为实现的是Public/Subscribe工作模式，所以不需要指定，第三个参数是发送的消息内容，接收Object类型
```



```java
//异常信息
SimpleMessageConverter only supports String, byte[] and Serializable payloads, received: com.szjm.itheimachapter08.entity.User$$EnhancerBySpringCGLIB$$5d20338a
//进行消息转化时默认使用了SimpleMessageConverter转换器进行消息转换存储，该转换器只支持Spring,byte[]和Serializable序列化后的消息。
//两种解决方法：
    一、将实体类实现JDK自带的Serializable序列化接口
    二、定制其他类型的消息转换器
```

```java
//消息转换器
@Configuration
public class RabbitMQConfig{
    @Bean
    public MessageConverter messageConverter(){
        return new Jackson2JsonMessageConverter();
    }
}
```

