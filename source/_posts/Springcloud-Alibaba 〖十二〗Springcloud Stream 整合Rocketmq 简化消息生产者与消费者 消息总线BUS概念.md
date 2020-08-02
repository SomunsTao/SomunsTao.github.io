---
title: Springcloud-Alibaba 〖十二〗Stream 整合Rocketmq 简化消息生产者与消费者 消息总线BUS概念
date: 2020-04-12
tags: Springcloud-Alibaba
categories: Springcloud-Alibaba
keywords: spring
description: Springcloud-Alibaba
---



# Springcloud-Alibaba 〖十二〗Springcloud Stream 整合Rocketmq 简化消息生产者与消费者 消息总线BUS概念



### Springcloud-Alibaba 〖十二〗Springcloud Stream 整合Rockermq

- - - [PS: github仓库[仓库地址](https://github.com/ktoking/springcloud-alibaba)项目都放到里面了](https://blog.csdn.net/kingtok/article/details/105195944#PS_githubhttpsgithubcomktokingspringcloudalibaba_1)

    - [一. 消息总线先放放,感觉现在用不到](https://blog.csdn.net/kingtok/article/details/105195944#__3)

    - [二. Springcloud Stream](https://blog.csdn.net/kingtok/article/details/105195944#_Springcloud_Stream_19)

    - [没Rocketmq基础的同学来这: [来看这里!!!](https://blog.csdn.net/kingtok/article/details/104212625)之前早已准备好的博客~~](https://blog.csdn.net/kingtok/article/details/105195944#Rocketmq_httpsblogcsdnnetkingtokarticledetails104212625_22)

    - [三. 新建module cloud-stream-rocketmq-provider8801 消息提供者](https://blog.csdn.net/kingtok/article/details/105195944#_module_cloudstreamrocketmqprovider8801__27)

    - - [3.1 项目结构](https://blog.csdn.net/kingtok/article/details/105195944#31__28)

      - [3.2 pom](https://blog.csdn.net/kingtok/article/details/105195944#32_pom_30)

      - [3.3 application.yml](https://blog.csdn.net/kingtok/article/details/105195944#33_applicationyml_89)

      - [3.5 主启动类](https://blog.csdn.net/kingtok/article/details/105195944#35__123)

      - [3.6 业务类](https://blog.csdn.net/kingtok/article/details/105195944#36__142)

      - - [controller层](https://blog.csdn.net/kingtok/article/details/105195944#controller_143)
        - [service接口](https://blog.csdn.net/kingtok/article/details/105195944#service_166)
        - [实现类](https://blog.csdn.net/kingtok/article/details/105195944#_176)

      - [3.7 测试](https://blog.csdn.net/kingtok/article/details/105195944#37__206)

    - [四. 新建模块cloud-stream-rabbitmq-consumer8802为消费者](https://blog.csdn.net/kingtok/article/details/105195944#_cloudstreamrabbitmqconsumer8802_268)

    - - [4.1 pom](https://blog.csdn.net/kingtok/article/details/105195944#41_pom_269)

      - [4.2 application.yml](https://blog.csdn.net/kingtok/article/details/105195944#42_applicationyml_326)

      - [4.3 主启动类](https://blog.csdn.net/kingtok/article/details/105195944#43__362)

      - [4.4 业务类](https://blog.csdn.net/kingtok/article/details/105195944#44__380)

      - - [controller层](https://blog.csdn.net/kingtok/article/details/105195944#controller_381)

      - [4.5 测试](https://blog.csdn.net/kingtok/article/details/105195944#45__407)

      - [4.8 yml详解(非本项目)](https://blog.csdn.net/kingtok/article/details/105195944#48_yml_414)

      - [4.7 重复消息](https://blog.csdn.net/kingtok/article/details/105195944#47__468)

    - [Springcloud Stream篇完,本篇使用的是Rocketmq,简化了MQ底层的操作,配置了一套简洁版的消息服务~](https://blog.csdn.net/kingtok/article/details/105195944#Springcloud_StreamRocketmqMQ_474)

    - [转载请标注~](https://blog.csdn.net/kingtok/article/details/105195944#_475)



### PS: github仓库[仓库地址](https://github.com/ktoking/springcloud-alibaba)项目都放到里面了

**强烈建议先去看我的Rocketmq: [Rocketmq集群搭建](https://blog.csdn.net/kingtok/article/details/104212625)**

### 一. 消息总线先放放,感觉现在用不到

**给大家看看原理**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200330115224889.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200330120421585.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
Spring Cloud Bus是用来将分布式系统的节点与轻量级消息系统链接起来的框架,
它整合了Java的事件处理机制和消息中间件的功能。Spring Clud Bus目前支持**RabbitMQ和Kafka。**

**什么是总线？**

在微服务架构的系统中，通常会使用轻量级的消息代理来构建一个共用的消息主题，并让系统中所有微服务实例都连接上来。由于该主题中产生的消息会被所有实例监听和消费，所以称它为消息总线。在总线上的各个实例，都可以方便的广播一些需要让其他连接在该主题上的实例都知道的消息。

**基本原理**

ConfigClient 实例都监听MQ中同一个topic（默认是springcloubus），当一个服务刷新数据的时候，它会把这个信息放入到Topic中，这样其他监听统一topic的服务就能得到通知，然后去更新自身的配置。

### 二. Springcloud Stream

**这里头比较铁啊,因为用的是springcloud-alibaba,所以自然肯定要用巴巴的rocketmq啊,而且之前还学过,配过集群环境与控制台,那我们就尝试一下Rocketmq嘻嘻~**

### 没Rocketmq基础的同学来这: [来看这里!!!](https://blog.csdn.net/kingtok/article/details/104212625)之前早已准备好的博客~~

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020033019132858.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**stream遵循发布订阅模式**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200330194049594.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

### 三. 新建module cloud-stream-rocketmq-provider8801 消息提供者

#### 3.1 项目结构

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200330215615987.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 3.2 pom

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>cloud2020</artifactId>
        <groupId>com.aiguigu.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-stream-rocketmq-provider8801</artifactId>

    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-stream</artifactId>
        </dependency>
        <!--stream-rocketmq-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-stream-binder-rocketmq</artifactId>
            <version>0.2.1.RELEASE</version>
    </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--基础配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>

1234567891011121314151617181920212223242526272829303132333435363738394041424344454647484950515253545556
```

#### 3.3 application.yml

```yml
server:
  port: 8801

spring:
  cloud:
    stream:
      rocketmq:
        binder:
          namesrv-addr: 公网IP1:9876,公网IP2:9876
      # 定义name为output的binding
      bindings:
        output: # 定义name为output的binding
          destination: springboot-mq #相当于topic
          content-type: application/json
        # 定义name为input的binding
#        input1:
#          destination: test-topic
#          content-type: application/json
#          group: test-group

eureka:
  client: #客户端进行eureka注册的配置
    service-url:
      defaultZone: http://localhost:7001/eureka
  instance:
    lease-renewal-interval-in-seconds: 2  #设置心跳的时间间隔（默认是30秒）
    lease-expiration-duration-in-seconds: 5 #如果现在超过了5秒的间隔（默认是90秒）
    instance-id: send-8801.com  #在信息列表时显示主机名称
    prefer-ip-address: true     #访问的路径变为IP地址

123456789101112131415161718192021222324252627282930
```

#### 3.5 主启动类

```java
package com.atguigu.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;


@EnableEurekaClient
@SpringBootApplication
public class StreamMQMain8801 {
    public static void main(String[] args) {
        SpringApplication.run(StreamMQMain8801.class,args);
    }
}

123456789101112131415
```

#### 3.6 业务类

##### controller层

```java
package com.atguigu.springcloud.controller;

import com.atguigu.springcloud.service.IMessageProvider;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;

@RestController
public class SendMessageController {
    @Resource
    private IMessageProvider messageProvider;

    @GetMapping("/sendMessage")
    public String sendMessage(){
        return messageProvider.send();
    }
}

12345678910111213141516171819
```

##### service接口

```java
package com.atguigu.springcloud.service;

public interface IMessageProvider {
    public String send();
}

123456
```

##### 实现类

```java
package com.atguigu.springcloud.service.impl;

import com.atguigu.springcloud.service.IMessageProvider;
import org.springframework.cloud.stream.annotation.EnableBinding;
import org.springframework.cloud.stream.messaging.Source;
import org.springframework.messaging.MessageChannel;
import org.springframework.messaging.support.MessageBuilder;

import javax.annotation.Resource;
import java.util.UUID;

@EnableBinding(Source.class)
public class IMessageProviderImpl implements IMessageProvider {

    @Resource
    private MessageChannel output; //消息发送管道

    @Override
    public String send() {
        String serial = UUID.randomUUID().toString();
        output.send(MessageBuilder.withPayload(serial).build());
        System.out.println("*****serial: " +serial);
        return null;
    }

}
1234567891011121314151617181920212223242526
```

#### 3.7 测试

**这里测试类,我们用之前Rocketmq章结用的一个consumer来监听这个topic下所有tags**
**也可以不用一下测试类,因为还要导pom,之后再写一个8802项目作为服务消费者**

```xml
 		 <dependency>
            <groupId>org.apache.rocketmq</groupId>
            <artifactId>rocketmq-client</artifactId>
            <version>4.4.0</version>
        </dependency>
12345
package com.itheima.mq.rocketmq.base.consumer;

import org.apache.rocketmq.client.consumer.DefaultMQPushConsumer;
import org.apache.rocketmq.client.consumer.listener.ConsumeConcurrentlyContext;
import org.apache.rocketmq.client.consumer.listener.ConsumeConcurrentlyStatus;
import org.apache.rocketmq.client.consumer.listener.MessageListenerConcurrently;
import org.apache.rocketmq.common.message.MessageExt;
import org.apache.rocketmq.common.protocol.heartbeat.MessageModel;

import java.util.List;

/**
 * 消息的接受者
 */
public class Consumer {

    public static void main(String[] args) throws Exception {
        //1.创建消费者Consumer，制定消费者组名
        DefaultMQPushConsumer consumer = new DefaultMQPushConsumer("group1");
        //2.指定Nameserver地址
        consumer.setNamesrvAddr("一号服务器公网IP:9876;二号服务器公网IP:9876");
        //3.订阅主题Topic和Tag
        consumer.subscribe("springboot-mq", "*");

        consumer.setMessageModel(MessageModel.BROADCASTING);

        consumer.registerMessageListener(new MessageListenerConcurrently() {
            @Override
            public ConsumeConcurrentlyStatus consumeMessage(List<MessageExt> list, ConsumeConcurrentlyContext consumeConcurrentlyContext) {

                for (MessageExt messageExt : list) {
                    String rt=new String(messageExt.getBody());
                    System.out.println(rt);
                }
                return ConsumeConcurrentlyStatus.CONSUME_SUCCESS;
            }
        });
        //5.启动消费者consumer
        consumer.start();
    }
}

123456789101112131415161718192021222324252627282930313233343536373839404142
```

**这里我们访问服务 http://localhost:8801/sendMessage,这时就会像我们Rocketmq里发一条消息,我们用consumer就可以实时的监听到**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200330222349974.png)
**这里是打印出了Message所有信息**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200330220954697.png)
**这里打印出了刚发送的消息**
![在这里插入图片描述](https://img-blog.csdnimg.cn/202003302224003.png)

### 四. 新建模块cloud-stream-rabbitmq-consumer8802为消费者

#### 4.1 pom

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>cloud2020</artifactId>
        <groupId>com.aiguigu.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>cloud-stream-rocketmq-consumer8802</artifactId>


    <dependencies>
        <!--stream-rocketmq-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-stream-binder-rocketmq</artifactId>
            <version>0.2.1.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--基础配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>

1234567891011121314151617181920212223242526272829303132333435363738394041424344454647484950515253
```

#### 4.2 application.yml

```yml
server:
  port: 8802

spring:
  application:
    name: rocketmq-consumer
  cloud:
    stream:
      rocketmq:
        binder:
          namesrv-addr: 一号服务器IP:9876;二号服务器IP:9876
      # 定义name为output的binding
      bindings:
#        output: # 定义name为output的binding
#          destination: springboot-mq
#          content-type: application/json
        # 定义name为input的binding
        input:
          destination: springboot-mq  #消费哪个topic
          content-type: application/json
          group: group1  #定义消费者组名

eureka:
  client: #客户端进行eureka注册的配置
    service-url:
      defaultZone: http://localhost:7001/eureka
  instance:
    lease-renewal-interval-in-seconds: 2  #设置心跳的时间间隔（默认是30秒）
    lease-expiration-duration-in-seconds: 5 #如果现在超过了5秒的间隔（默认是90秒）
    instance-id: recive-8802.com  #在信息列表时显示主机名称
    prefer-ip-address: true     #访问的路径变为IP地址

1234567891011121314151617181920212223242526272829303132
```

#### 4.3 主启动类

```java
package com.atguigu.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@SpringBootApplication
@EnableEurekaClient
public class StreamMQmMain8802 {
    public static void main(String[] args) {
        SpringApplication.run(StreamMQmMain8802.class,args);
    }
}

1234567891011121314
```

#### 4.4 业务类

##### controller层

```java
package com.atguigu.springcloud.controller;


import org.springframework.beans.factory.annotation.Value;
import org.springframework.cloud.stream.annotation.EnableBinding;
import org.springframework.cloud.stream.annotation.StreamListener;
import org.springframework.cloud.stream.messaging.Sink;
import org.springframework.messaging.Message;
import org.springframework.stereotype.Component;

@Component
@EnableBinding(Sink.class)
public class ReceiveMessageListenerController {
    @Value("${server.port}")
    private String serverPort;

    @StreamListener(Sink.INPUT)
    public void input(Message<String> message){
        System.out.println("消费者1号，------->接收到的消息： "+message.getPayload()+"\t port: "+serverPort);
    }
}

12345678910111213141516171819202122
```

#### 4.5 测试

**我们把7001注册中心,8801生产者,8802消费者都启动起来,这时我们去调用8801的方法生成随机订单号发给消息队列,8802立马监听到了相同topic下的消息,并把消息打在公屏上**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200330225322181.png)
![8801](https://img-blog.csdnimg.cn/20200330225337884.png)
![8802](https://img-blog.csdnimg.cn/20200330225402124.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**这时候我们rocketmq控制台也有了相对应的曲线与分析**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200330225636174.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 4.8 yml详解(非本项目)

**消费者**

```yml
spring:
    cloud:
        stream:
            default-binder: rocketmq
            rocketmq:
                binder:
                    name-server: 192.168.1.179:9876
                bindings:
                    #自定义的名称 对应spring.cloud.stream.bindings.input1
                    
                    input1:
                        consumer:
                            tags: test-tag1 # 订阅的tag，二级分类
                            orderly: false  # 是否按顺序消费
            bindings:
                #自定义的名称
                input1:
                    destination: test-topic-user # 订阅的topic ,一级分类
                    content-type: application/json
                    group: test-input-group1    #group
                    consumer:
                        concurrency: 20
                        maxAttempts: 1
1234567891011121314151617181920212223
```

**生产者**

```yml
spring:
    cloud:
        stream:
            default-binder: rocketmq
            rocketmq:
                binder:
                    #rocketmq地址
                    name-server: 192.168.0.78:9876
                bindings:
                    #自定义的名称 对应spring.cloud.stream.bindings.output1
                    output1:
                        producer:
                            group: test-group-user-ouput1
                            sync: true
            #Binding: 包括 Input Binding 和 Output Binding。
            #Binding 在消息中间件与应用程序提供的 Provider 和 Consumer 之间提供了一个桥梁，
            #实现了开发者只需使用应用程序的 Provider 或 Consumer 生产或消费数据即可，屏蔽了开发者与底层消息中间件的接触。
            bindings:
                #自定义的名称
                output1:
                    destination: test-topic-user   # topic(一级分类)
                    content-type: application/json
12345678910111213141516171819202122
```

#### 4.7 重复消息

**微服务应用放置于同一个group中，就能够保证消息只会被其中一个应用消费一次。**
**不同的组是可以消费的，同一个组内会发生竞争关系，只有其中 一个可以消费。**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200331112900219.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**就是负载均衡的意思~指定相同组的消费者负载均衡的消费消息**

### Springcloud Stream篇完,本篇使用的是Rocketmq,简化了MQ底层的操作,配置了一套简洁版的消息服务~

### 转载请标注~