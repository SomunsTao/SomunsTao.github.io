---
title: Springcloud-Alibaba 〖八〗OpenFeign篇
date: 2020-04-08
tags: Springcloud-Alibaba
categories: Springcloud-Alibaba
keywords: spring
description: Springcloud-Alibaba
---



# Springcloud-Alibaba 〖八〗OpenFeign篇

### Springcloud-Alibaba 〖八〗OpenFeign篇

- - - [PS: github仓库[仓库地址](https://github.com/ktoking/springcloud-alibaba)项目都放到里面了](https://blog.csdn.net/kingtok/article/details/105090682#PS_githubhttpsgithubcomktokingspringcloudalibaba_1)

    - [一. 什么是Feign?](https://blog.csdn.net/kingtok/article/details/105090682#_Feign_2)

    - [二. OpenFeign与Feign区别](https://blog.csdn.net/kingtok/article/details/105090682#_OpenFeignFeign_11)

    - [三. 服务调用cloud-consumer-feign-order80模块](https://blog.csdn.net/kingtok/article/details/105090682#_cloudconsumerfeignorder80_34)

    - - [3.1 目录结构](https://blog.csdn.net/kingtok/article/details/105090682#31__35)
      - [3.2 改pom](https://blog.csdn.net/kingtok/article/details/105090682#32_pom_37)
      - [3.3建 application.yml 配置文件](https://blog.csdn.net/kingtok/article/details/105090682#33_applicationyml__106)
      - [3.4 主启动类](https://blog.csdn.net/kingtok/article/details/105090682#34__118)
      - [3.5 业务类](https://blog.csdn.net/kingtok/article/details/105090682#35__138)
      - [3.6 测试](https://blog.csdn.net/kingtok/article/details/105090682#36__191)

    - [四. 超时控制](https://blog.csdn.net/kingtok/article/details/105090682#__194)

    - [五. 日志打印](https://blog.csdn.net/kingtok/article/details/105090682#__207)

    - - [5.1 日志功能](https://blog.csdn.net/kingtok/article/details/105090682#51__208)
      - [5.2 日志级别](https://blog.csdn.net/kingtok/article/details/105090682#52__213)
      - [5.3 添加配置类](https://blog.csdn.net/kingtok/article/details/105090682#53__220)

    - [OpenFeign篇完,点赞+关注!](https://blog.csdn.net/kingtok/article/details/105090682#OpenFeign_250)

    - [白嫖什么的不存在的~](https://blog.csdn.net/kingtok/article/details/105090682#_251)



### PS: github仓库[仓库地址](https://github.com/ktoking/springcloud-alibaba)项目都放到里面了

### 一. 什么是Feign?

**Feign** 是一个声明式 WebService 客户端。使用 Feign 能让编写Web Service 客户端更加简单。

**Feign 旨在使编写Java Http 客户端变得更容易。**
前面在使用 Ribbon+RestTemplate时，利用RestTemplate 对http请求的封装处理，形成了一套模板化的调用方法。但是在实际开发中，由于对服务依赖的调用可能不止一处，往往一个接口会被多处调用，所以通常都会针对每个微服务自行封装一些客户端类来包装这些依赖服务的调用。所以，Feign 在此基础上做了进一步封装，由他来帮助我们定义和实现依赖服务接口的定义。在Feign 的实现下，我们只需创建一个接口并使用注解的方式来配置它（**以前是Dao接口上面标注Mapper注解，现在是一个微服务接口上面标注衣一个Feign注解即可**），即可完成对服务提供方的接口绑定，简化了使用Spring Cloud Ribbon时，自动封装服务调用客户端的开发量。

**Feign集成了Ribbon**
利用Ribbon维护了Payment 的服务列表信息，并且通过轮询实现了客户端的负载均衡。而与Ribbon不同的是，通过feign 只需要定义服务绑定接口且以声明式的方法，优雅而简单的实现了服务调用。

### 二. OpenFeign与Feign区别

| OpenFeign                                                    | Feign                                                        |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| OpenFeign是springcloud在Feign的基础上支持了SpringMVC的注解，如@RequestMapping等等。OpenFeign的@FeignClient可以解析SpringMVC的@RequestMapping注解下的接口，并通过动态代理的方式产生实现类，实现类中做负载均衡并调用其他服务。 | Feign是Springcloud组件中的一个轻量级Restful的HTTP服务客户端，Feign内置了Ribbon，用来做客户端负载均衡，去调用服务注册中心的服务。Feign的使用方式是：使用Feign的注解定义接口，调用这个接口，就可以调用服务注册中心的服务 |

**OpenFeign**

```xml
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency> 
1234
```

**Feign**

```xml
 <dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-feign</artifactId>
</dependency>
1234
```

### 三. 服务调用cloud-consumer-feign-order80模块

#### 3.1 目录结构

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200325152340515.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 3.2 改pom

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

    <artifactId>cloud-consumer-feign-order80</artifactId>


    <dependencies>
        <!--openfeign-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
        <!--eureka client-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <!--引入自定义的api通用包，可以使用Payment支付Entity-->
        <dependency>
            <groupId>com.atguigu.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>${project.version}</version>
        </dependency>
        <!--web-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--一般基础通用配置-->
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
        <dependency>
            <groupId>com.aiguigu.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>1.0-SNAPSHOT</version>
            <scope>compile</scope>
        </dependency>
    </dependencies>
</project>
1234567891011121314151617181920212223242526272829303132333435363738394041424344454647484950515253545556575859606162636465
```

#### 3.3建 application.yml 配置文件

```yml
server:
  port: 80

eureka:
  client:
    register-with-eureka: false
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka
12345678
```

#### 3.4 主启动类

**要开启Feign就要在主启动类上加@EnableFeignClients**

```java
package com.atguigu.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.openfeign.EnableFeignClients;

@SpringBootApplication
@EnableFeignClients
public class OrderOpenFeignMain80 {
    public static void main(String[] args) {
        SpringApplication.run(OrderOpenFeignMain80.class,args);
    }
}

123456789101112131415
```

#### 3.5 业务类

**service接口**

这里对应的是我们8001/8002 服务的service
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200325153659365.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

```java
package com.atguigu.springcloud.service;

import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.stereotype.Component;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import com.atguigu.springcloud.entities.CommonResult;

@FeignClient(value = "cloud-payment-service")
@Component
public interface PaymentFeignService {

    @GetMapping(value = "/payment/get/{id}")
    public CommonResult getPaymentById(@PathVariable("id") Long id);
}

12345678910111213141516
```

**这里只实现接口,并不实现方法,因为我们是去8001/8002集群去调用服务**

**Controller层**

```java
package com.atguigu.springcloud.controller;

import com.atguigu.springcloud.entities.CommonResult;
import com.atguigu.springcloud.service.PaymentFeignService;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;

@RestController
public class OrderFeignController {

    @Resource
    private PaymentFeignService paymentFeignService;

    @GetMapping(value = "/consumer/payment/get/{id}")
    public CommonResult getPaymentById(@PathVariable("id") Long id){
        CommonResult paymentById = paymentFeignService.getPaymentById(id);
        return paymentById ;
    }
}

1234567891011121314151617181920212223
```

#### 3.6 测试

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200325153846899.png)

### 四. 超时控制

**由于Feign天生支持Ribbon所以在超时控制这块由Ribbon来控制**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200325155550508.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**添加配置文件(默认1s)**

```yml
#设置feign 客户端超时时间（openFeign默认支持ribbon）
ribbon:
  #指的是建立连接后从服务器读取到可用资源所用的时间
  ReadTimeout: 5000
  #指的是建立连接所用的时间，适用于网络状况正常的情况下，两端连接所用的时间
  ConnectTimeout: 5000

1234567
```

### 五. 日志打印

#### 5.1 日志功能

Feign 提供了日志打印功能，可以通过配置来调整日志级别，从而了解 Feign 中 Http 请求的细节。
说白了就是对接口的调用情况进行监控和输出

#### 5.2 日志级别

- **NONE：默认的，不显示任何日志**
- **BASIC：仅记录请求方法、URL、响应状态码及执行时间**
- **HEADERS：除了 BASIC 中定义的信息之外，还有请求和响应的头信息**
- **FULL：除了 HEADERS 中定义的信息之外，还有请求和响应的正文及元数据**

#### 5.3 添加配置类

**添加一个配置类**![在这里插入图片描述](https://img-blog.csdnimg.cn/2020032516091836.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

```java
package com.atguigu.springcloud.config;

import feign.Logger;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class FeignConfig {
    @Bean
    Logger.Level feignloggerLevel(){
        return Logger.Level.FULL;
    }
}

1234567891011121314
```

**增加配置文件 application.yml**

```yml
logging:
  level:
    #feign日志以什么级别监控哪个接口
    com.atguigu.springcloud.service.PaymentFeignService: debug
1234
```

**要暴露哪个服务,把服务的全限定名加到level配置文件下面**

### OpenFeign篇完,点赞+关注!

### 白嫖什么的不存在的~