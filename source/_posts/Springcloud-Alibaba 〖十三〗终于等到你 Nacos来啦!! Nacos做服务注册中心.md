---
title: Springcloud-Alibaba 〖十三〗终于等到你 Nacos来啦!! Nacos做服务注册中心
date: 2020-04-13
tags: Springcloud-Alibaba
categories: Springcloud-Alibaba
keywords: spring
description: Springcloud-Alibaba
---



# Springcloud-Alibaba 〖十三〗终于等到你 Nacos来啦!! Nacos做服务注册中心



### Springcloud-Alibaba 〖十三〗Nacos来啦!!

- - - [PS: github仓库[仓库地址](https://github.com/ktoking/springcloud-alibaba)项目都放到里面了](https://blog.csdn.net/kingtok/article/details/105230295#PS_githubhttpsgithubcomktokingspringcloudalibaba_1)

    - [一. Springcloud-Alibaba](https://blog.csdn.net/kingtok/article/details/105230295#_SpringcloudAlibaba_2)

    - [二. Nacos安装](https://blog.csdn.net/kingtok/article/details/105230295#_Nacos_14)

    - [三. Nacos做服务注册中心 9001项目](https://blog.csdn.net/kingtok/article/details/105230295#_Nacos_9001_26)

    - - [3.1 项目目录](https://blog.csdn.net/kingtok/article/details/105230295#31__30)

      - [3.2 pom](https://blog.csdn.net/kingtok/article/details/105230295#32_pom_32)

      - [3.3 application.yml](https://blog.csdn.net/kingtok/article/details/105230295#33_applicationyml_82)

      - [3.4 主启动](https://blog.csdn.net/kingtok/article/details/105230295#34__102)

      - [3.5 业务类](https://blog.csdn.net/kingtok/article/details/105230295#35__120)

      - - [controller层](https://blog.csdn.net/kingtok/article/details/105230295#controller_121)

      - [3.6 测试](https://blog.csdn.net/kingtok/article/details/105230295#36__147)

    - [四. 集群测试](https://blog.csdn.net/kingtok/article/details/105230295#__154)

    - - [4.1 配置集群(偷懒的办法~)](https://blog.csdn.net/kingtok/article/details/105230295#41__155)

      - [4.2 测试](https://blog.csdn.net/kingtok/article/details/105230295#42__161)

      - [4.3 为了更好演示集群,这里还是重新创建一个9002项目](https://blog.csdn.net/kingtok/article/details/105230295#43_9002_168)

      - - [4.3.1 项目目录](https://blog.csdn.net/kingtok/article/details/105230295#431__169)
        - [4.3.2 建pom](https://blog.csdn.net/kingtok/article/details/105230295#432_pom_172)
        - [4.3.3 application.yml](https://blog.csdn.net/kingtok/article/details/105230295#433_applicationyml_173)
        - [4.3.4 主启动](https://blog.csdn.net/kingtok/article/details/105230295#434__174)
        - [4.3.5 业务类(controller层)](https://blog.csdn.net/kingtok/article/details/105230295#435_controller_175)
        - [4.3.6 测试](https://blog.csdn.net/kingtok/article/details/105230295#436__176)

    - [五. 新建module cloudalibaba-consumer-nacos-order83](https://blog.csdn.net/kingtok/article/details/105230295#_module_cloudalibabaconsumernacosorder83_179)

    - - [5.1 项目目录](https://blog.csdn.net/kingtok/article/details/105230295#51__182)
      - [5.2 pom](https://blog.csdn.net/kingtok/article/details/105230295#52_pom_184)
      - [5.3 application.yml](https://blog.csdn.net/kingtok/article/details/105230295#53_applicationyml_231)
      - [5.4 主启动类](https://blog.csdn.net/kingtok/article/details/105230295#54__251)
      - [5.5 业务类](https://blog.csdn.net/kingtok/article/details/105230295#55__269)
      - [5.6 测试](https://blog.csdn.net/kingtok/article/details/105230295#56__299)

    - [六. 总结](https://blog.csdn.net/kingtok/article/details/105230295#__305)

    - [Nacos确实比较方便而且功能强大!下一篇会讲Nacos作为服务配置中心~](https://blog.csdn.net/kingtok/article/details/105230295#NacosNacos_313)

    - [关注不迷路,点赞走一波~ 转载请标注~](https://blog.csdn.net/kingtok/article/details/105230295#__314)



### PS: github仓库[仓库地址](https://github.com/ktoking/springcloud-alibaba)项目都放到里面了

### 一. Springcloud-Alibaba

**官网地址:**[Springcloud-Alibaba](https://github.com/alibaba/spring-cloud-alibaba/blob/master/README-zh.md)

- **服务限流降级**: 默认支持Servlet. Feign. RestTemplate、 Dubbo 和RocketMQ限流降级功能的接入，可以在运行时通过控制台实时修改限流降
  级规则，还支持查看限流降级Metrics监控。
- **服务注册与发现**: 适配Spring Cloud服务注册与发现标准,默认集成了Ribbon的支持。
- **分布式配置管理**: 支持分布式系统中的外部化配置，配置更改时自动刷新。
- **消息驱动能力**: Spring Cloud Stream为微服务应用构建消息驱动能力。阿里云对象存储:阿里云提供的海量、安全、低成本、可靠的云存储服务。支持在任何应用、任何时间、任何地点存储和访问任意类型的数据。
- **分布式任务调度**: 提供秒级、精准、可靠可用的定时(基于Cron表达式)任务调度服务。同时提供分布式的任务执行模型，如网格任务。网格任务支持海量子任务均匀分配到所有Worker (schedulerx- client). 上执行。
- **阿里云对象存储**：阿里云提供的海量、安全、低成本、高可靠的云存储服务。支持在任何应用、任何时间、任何地点存储和访问任意类型的数据。
- **阿里云短信服务**：覆盖全球的短信服务，友好、高效、智能的互联化通讯能力，帮助企业迅速搭建客户触达通道。

### 二. Nacos安装

前四个字母分别为Naming和Configuration的前两个字母，最后的s为Service。

**Nacos就是注册中心+配置中心的组合 Nacos=Eureka+Config+Bus**

**Nacos**:[官网下载地址](https://github.com/alibaba/nacos/releases/tag/1.1.4)

**下载解压后,找到nacos/bin目录下的startup.cmd命令点开**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200331201648832.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200331201807701.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**默认的账号密码都是 nacos,进入local:8848/nacos就可以进入我们的控制台**
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020033120194142.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

### 三. Nacos做服务注册中心 9001项目

**首先你的父POM一定要有Springcloud-Alibaba依赖**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200331214253401.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 3.1 项目目录

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200331220622292.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

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

    <artifactId>cloudalibaba-provider-payment9001</artifactId>

    <!--本pom.xml引入nacos-->
    <dependencies>
        <!--springcloud alibaba nacos-->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

</project>
1234567891011121314151617181920212223242526272829303132333435363738394041424344454647
```

#### 3.3 application.yml

```yml
server:
  port: 9001

spring:
  application:
    name: nacos-payment-provider
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848 #配置Nacos地址

management:
  endpoints:
    web:
      exposure:
        include: '*'  #监控

1234567891011121314151617
```

#### 3.4 主启动

```java
package com.atguigu.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

@SpringBootApplication
@EnableDiscoveryClient
public class PaymentMain9001 {
    public static void main(String[] args) {
        SpringApplication.run(PaymentMain9001.class,args);
    }
}

1234567891011121314
```

#### 3.5 业务类

##### controller层

```java
package com.atguigu.springcloud.controller;

import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

@RestController
@Slf4j
public class PaymentController {

    @Value("${server.port}")
    private String serverPort;

    @GetMapping("/payment/nacos/{id}")
    public String getPayment(@PathVariable("id") Integer id){
        return "nacos registry,serverPort: "+ serverPort+"\t id"+id;
    }

}

12345678910111213141516171819202122
```

#### 3.6 测试

**首先来测试方法,可以访问到**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200331220853510.png)
**这时候我们来访问Nacos的UI界面,发现服务已经注册上来**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200331220913970.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**点开详情可以看见更多信息**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200331220923221.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

### 四. 集群测试

#### 4.1 配置集群(偷懒的办法~)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200331221336397.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**这时候可以从已经配置过的9001复制一份模板来**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200331221645124.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**这时候我们启动9011服务**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200331221708648.png)

#### 4.2 测试

**打开我们的Nacos控制台发现实例数量为2**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200331221827749.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**测试方法9001**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200331221856820.png)
**测试方法9011**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200331221922825.png)

#### 4.3 为了更好演示集群,这里还是重新创建一个9002项目

##### 4.3.1 项目目录

**基本与9001一样,只是端口号不一样,所以以下只贴出步骤,不演示(一模一样的还让我演示?orz)**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200331223226946.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

##### 4.3.2 建pom

##### 4.3.3 application.yml

##### 4.3.4 主启动

##### 4.3.5 业务类(controller层)

##### 4.3.6 测试

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200331223608230.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

### 五. 新建module cloudalibaba-consumer-nacos-order83

**Nacos天生支持负载均衡因为依赖引入了ribbon**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200331224031926.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 5.1 项目目录

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200331225655637.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 5.2 pom

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

    <artifactId>cloudalibaba-consumer-nacos-order83</artifactId>
    <dependencies>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
        <dependency>
            <groupId>com.aiguigu.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>1.0-SNAPSHOT</version>
            <scope>compile</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
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
12345678910111213141516171819202122232425262728293031323334353637383940414243
```

#### 5.3 application.yml

```yml
server:
  port: 83

spring:
  application:
    name: nacos-order-consumer
  cloud:
    nacos:
      discovery:
        server-addr: localhost:8848

#消费者将要去访问的微服务名称（成功注册进nacos的微服务提供者），在这配置了访问的服务，业务类就不用在定义常量了
service-url:
  nacos-user-service: http://nacos-payment-provider

123456789101112131415
```

#### 5.4 主启动类

```java
package com.atguigu.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

@SpringBootApplication
@EnableDiscoveryClient
public class OrderNacosMain83 {
    public static void main(String[] args) {
        SpringApplication.run(OrderNacosMain83.class,args);
    }
}
12345678910111213
```

#### 5.5 业务类

**controller层**

```java
package com.atguigu.springcloud.alibaba.controller;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;
import javax.annotation.Resource;
@RestController
public class OrderNacosController {
    /*
    因为在yml中配置了service-url.nacos-user-service，
    这里不需要再定义要访问微服务名常量，而是通过boot直接读出来
     */
    @Value("${service-url.nacos-user-service}")
    private String serverURL;

    @Resource
    private RestTemplate restTemplate;

    @GetMapping("/consumer/payment/nacos/{id}")
    public String paymentInfo(@PathVariable("id") Long id){
        return restTemplate.getForObject(serverURL+"/payment/nacos/"+id,String.class);
    }
}

12345678910111213141516171819202122232425
```

#### 5.6 测试

**这里我们在Nacos看见消费者已经注册进来了**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200331225931625.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**这时我们来测试负载均衡,访问controller层方法一次是9001,一次是9002,负载均衡测试成功!**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200331225909578.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200331225915932.png)

### 六. 总结

| 组件名    | 语言 | CAP   | 服务监控检查 | 对外暴露接口 | Springcloud集成 |
| --------- | ---- | ----- | ------------ | ------------ | --------------- |
| Eureka    | Java | AP    | 可配支持     | HTTP         | 已集成          |
| Consul    | Go   | CP    | 支持         | HTTP/DNS     | 已集成          |
| Zookeeper | Java | CP    | 支持         | 客户端       | 已集成          |
| Nacos     | Java | AP/CP | 支持         | HTTP/DNS/UDP | 已集成          |

### Nacos确实比较方便而且功能强大!下一篇会讲Nacos作为服务配置中心~

### 关注不迷路,点赞走一波~ 转载请标注~