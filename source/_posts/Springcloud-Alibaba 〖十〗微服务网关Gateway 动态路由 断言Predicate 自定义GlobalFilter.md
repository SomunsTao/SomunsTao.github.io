---
title: Springcloud-Alibaba 〖十〗微服务网关Gateway 动态路由 断言Predicate 自定义GlobalFilter
date: 2020-04-10
tags: Springcloud-Alibaba 
categories: Springcloud-Alibaba 
keywords: spring
description: Springcloud-Alibaba 
---

# Springcloud-Alibaba 〖十〗微服务网关Gateway 动态路由 断言Predicate 自定义GlobalFilter



### Springcloud-Alibaba 〖十〗微服务网关Gateway

- - - [PS: github仓库[仓库地址](https://github.com/ktoking/springcloud-alibaba)项目都放到里面了](https://blog.csdn.net/kingtok/article/details/105161964#PS_githubhttpsgithubcomktokingspringcloudalibaba_1)

    - [一. 什么是Gateway网关?](https://blog.csdn.net/kingtok/article/details/105161964#_Gateway_2)

    - [二. Spring Cloud Gateway具有如下特性](https://blog.csdn.net/kingtok/article/details/105161964#_Spring_Cloud_Gateway_14)

    - [三. 新建module cloud-gateway-gateway9527](https://blog.csdn.net/kingtok/article/details/105161964#_module_cloudgatewaygateway9527_25)

    - - [3.1 项目目录](https://blog.csdn.net/kingtok/article/details/105161964#31__26)
      - [3.2 pom](https://blog.csdn.net/kingtok/article/details/105161964#32_pom_28)
      - [3.3 application.yml](https://blog.csdn.net/kingtok/article/details/105161964#33_applicationyml_84)
      - [3.4 主启动](https://blog.csdn.net/kingtok/article/details/105161964#34__121)
      - [3.5 测试](https://blog.csdn.net/kingtok/article/details/105161964#35__140)
      - [3.6 路由的另外一种配置方法](https://blog.csdn.net/kingtok/article/details/105161964#36__147)

    - [四. 动态路由](https://blog.csdn.net/kingtok/article/details/105161964#__175)

    - [五. Gateway 常用的 Predicate](https://blog.csdn.net/kingtok/article/details/105161964#_Gateway__Predicate_221)

    - - [5.2 配置时间断言](https://blog.csdn.net/kingtok/article/details/105161964#52__225)
      - [5.3 Cookie断言](https://blog.csdn.net/kingtok/article/details/105161964#53_Cookie_245)
      - [5.4 Header断言](https://blog.csdn.net/kingtok/article/details/105161964#54_Header_254)
      - [5.5总结](https://blog.csdn.net/kingtok/article/details/105161964#55_262)

    - [六. Gateway的filter](https://blog.csdn.net/kingtok/article/details/105161964#_Gatewayfilter_267)

    - - [6.1 自定义全局GlobalFilter](https://blog.csdn.net/kingtok/article/details/105161964#61_GlobalFilter_277)

    - [Gateway篇完,这篇后半部分有些没写是因为太多了,大家可以自己先尝试,方法都大同小异,点赞+关注不迷路](https://blog.csdn.net/kingtok/article/details/105161964#Gateway_325)

    - [转载请标注~](https://blog.csdn.net/kingtok/article/details/105161964#_326)



### PS: github仓库[仓库地址](https://github.com/ktoking/springcloud-alibaba)项目都放到里面了

### 一. 什么是Gateway网关?

- **Gateway是在Spring生态系统之上构建的API网关服务,基于Spring 5,Spring Boot 2和Project Reactor等技术。**
- **Gateway旨在提供一 种简单而有效的方式来对API进行路由，以吸提供- 些强大的过滤器功能， 例如:熔断、限流、重试等**
- **SpringCloud Gateway是Spring Cloud的一个全新项目,纡Spring 5.0+ Spring Boot 2.0和Project Reactor等技术开发的网关,它旨在为微服务架构提供一种简单有效的统一 的API路由管理方式。**
- **SpringCloud Gateway作为Spring Cloud生态系统中的网关，目标是替代Zuul,在Spring Cloud 2.0以上版本中，没有对新版本的Zuul 2.0以上最新高性能版本进行集成，仍然还是使用的Zuul 1.x非Reactor模式的老版本。而为了提升网关的性能，SpringCloud Gateway是基于WebFlux框架实现的，而WebFlux框架底层则使用了高性能的Reactor模式通信框架Netty**
- **SpringCloud Gateway的目标提供统一的路由方式且基 于Filter 链的方式提供了网关基本的功能，例如:安全，监控/指标, 和限流。**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328153102686.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

### 二. Spring Cloud Gateway具有如下特性

- **基于Spring Framework 5, Project Reactor和Spring Boot 2.0进行构建;**
- **动态路由:能够匹配任何请求属性;**
- **可以对路由指定Predicate (断言)和Filter (过滤器) ;**
- **集成Hystrix的断路器功能;**
- **集成Spring Cloud服务发现功能;**
- **易于编写的Predicate (断言)和Filter (过滤器) ;**
- **请求限流功能;**
- **支持路径重写。**

### 三. 新建module cloud-gateway-gateway9527

#### 3.1 项目目录

![gateway](https://img-blog.csdnimg.cn/2020032816154593.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

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

    <artifactId>cloud-gateway-gateway9527</artifactId>

    <dependencies>
        <!--gateway-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-gateway</artifactId>
        </dependency>

        <!--eureka client-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <!--引入自定义的api通用包，可使用Payment支付Entity-->
        <dependency>
            <groupId>com.aiguigu.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>1.0-SNAPSHOT</version>
            <scope>compile</scope>
        </dependency>
        <!--一般基础配置类-->
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

12345678910111213141516171819202122232425262728293031323334353637383940414243444546474849505152
```

#### 3.3 application.yml

```yml
server:
  port: 9527

spring:
  application:
    name: cloud-gateway
  cloud:
    gateway:
      routes:
        - id: payment_routh #payment_routh    #路由的ID，没有固定规则但要求唯一，简易配合服务名
          uri: http://localhost:8001         #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/get/**          #断言，路径相匹配的进行路由

        - id: payment_routh2 #payment_routh   #路由的ID，没有固定规则但要求唯一，简易配合服务名
          uri: http://localhost:8001          #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/lb/**             #断言，路径相匹配的进行路由


eureka:
  instance:
    hostname: cloud-gateway-service
  client:
    fetch-registry: true
    register-with-eureka: true
    service-url:
      #设置与Eureka Server交互的地址查询服务和注册服务都需要依赖这个地址
      #      defaultZone: http://eureka7002.com:7002/eureka/
      #      单机版eureka
      defaultZone: http://eureka7001.com:7001/eureka/

1234567891011121314151617181920212223242526272829303132
```

#### 3.4 主启动

```java
package com.atguigu.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@SpringBootApplication
@EnableEurekaClient
public class GatewayMain9527 {

    public static void main(String[] args) {
        SpringApplication.run(GatewayMain9527.class,args);
    }
}

123456789101112131415
```

#### 3.5 测试

**打开我们的EurekaUI界面,发现服务已经注册进来**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328161806979.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**测试网关,发现不用之前的localhost:8001/payment/get/3,9527直接在外面包装了一层**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328161825974.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328162354112.png)

#### 3.6 路由的另外一种配置方法

**这里新建一个config**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328170459874.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**配置路由规则,代替yml配置**

```java
package com.atguigu.springcloud.config;

import org.springframework.cloud.gateway.route.RouteLocator;
import org.springframework.cloud.gateway.route.builder.RouteLocatorBuilder;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class GatewayConfig {
    @Bean
    public RouteLocator customRouteLocator(RouteLocatorBuilder routeLocatorBuilder){
        RouteLocatorBuilder.Builder routes = routeLocatorBuilder.routes();

        routes.route("path_route_atguigu",
                r -> r.path("/guonei").uri("http://news.baidu.com/guonei")).build();
        return routes.build();
    }
}

12345678910111213141516171819
```

**如果要访问 http://news.baidu.com/guonei 这个网站,配置路由规则为http://localhost:9527/guonei就可以访问到**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328170446984.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

### 四. 动态路由

**动态路由实现**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328221705727.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**9527网关配置修改为如下**

```yml
server:
  port: 9527

spring:
  application:
    name: cloud-gateway
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true #开启从注册中心动态动态创建路由的功能,利用微服务名进行路由
      routes:
        - id: payment_routh #payment_routh    #路由的ID，没有固定规则但要求唯一，简易配合服务名
          uri: lb://cloud-payment-service
#          uri: http://localhost:8001         #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/get/**          #断言，路径相匹配的进行路由

        - id: payment_routh2 #payment_routh   #路由的ID，没有固定规则但要求唯一，简易配合服务名
          uri: lb://cloud-payment-service
#          uri: http://localhost:8001          #匹配后提供服务的路由地址
          predicates:
            - Path=/payment/lb/**             #断言，路径相匹配的进行路由


eureka:
  instance:
    hostname: cloud-gateway-service
  client:
    fetch-registry: true
    register-with-eureka: true
    service-url:
      #设置与Eureka Server交互的地址查询服务和注册服务都需要依赖这个地址
      #      defaultZone: http://eureka7002.com:7002/eureka/
      #      单机版eureka
      defaultZone: http://eureka7001.com:7001/eureka/

12345678910111213141516171819202122232425262728293031323334353637
```

**测试结果如下,9527网关成功从Eureka注册中心找到并且实现了负载均衡**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328221834221.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328221847364.png)

### 五. Gateway 常用的 Predicate

**请求要满足断言表达式判断为true才可访问,只要一个不符合就回访问不到服务**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328222722537.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 5.2 配置时间断言

**在当前时间之后才可以访问**
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020032822344753.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**时间格式复杂怎么办?这里有生成当前时间的方法**

```java
import java.time.ZonedDateTime;

public class TimeBulider {
    public static void main(String[] args) {
        ZonedDateTime zonedDateTime=ZonedDateTime.now();
        System.out.println(zonedDateTime);
    }
}

123456789
```

**测试,如果我将时间调至明天,再次访问会出现找不到服务**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328223901932.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**可以看到这里断言还有After,Before和Between,方法都是大同小异**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328224037147.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 5.3 Cookie断言

Cookie Route Predicate需要两个参数, 一个是**Cookie name** ,一个是**正则表达式。**

**路由规则会通过获取对应的Cookie name值和正则表达式去匹配，如果匹配上就会执行路由，如果没有匹配上则不执行**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328225536833.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**这里配置一个Cookie 通过命令框的CURL去访问,出现了负载均衡**
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020032822515164.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 5.4 Header断言

```java
 - Header=X-Request-Id, \d+ 
 #请求头需要有X-Request-Id属性名,值为整数正则表达式
12
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328230144460.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**命令框输入http://localhost:9527/payment/lb -H “X-Request-Id:1234”,负数就直接报错**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328230535946.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 5.5总结

**所有断言不一一举例了**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328231039630.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**说白了,Predicate就是为了实现一组匹配规则,让请求过来找到对应的Route进行处理。**

### 六. Gateway的filter

**指的是Spring框架中GatewayFilter的实例，使用过滤器，可以在请求被路由前或者之后对请求进行修改。**

路由过滤器可用于修改进入的**HTTP请求和返回的HTTP响应**,路由过滤器只能指定路由进行使用。

Spring Cloud Gateway内置了多种路由过滤器，他们都由**GatewayFilter的工厂类来产生**

**常用的GatewayFilter 有31种之多**
**全局的Filter也有10种**

#### 6.1 自定义全局GlobalFilter

**创建Gateway配置类**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329115817873.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**实现一个GlobalFilter**

```java
package com.atguigu.springcloud.filter;

import lombok.extern.slf4j.Slf4j;
import org.springframework.cloud.gateway.filter.GatewayFilterChain;
import org.springframework.cloud.gateway.filter.GlobalFilter;
import org.springframework.core.Ordered;
import org.springframework.core.annotation.Order;
import org.springframework.http.HttpStatus;
import org.springframework.stereotype.Component;
import org.springframework.web.server.ServerWebExchange;
import reactor.core.publisher.Mono;

import java.util.Date;

@Component
@Slf4j
@Order(0)
public class MyLogGateWayFilter implements GlobalFilter{
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        log.info("***************Come in My LogGlobalFilter "+new Date());
        String uname = exchange.getRequest().getQueryParams().getFirst("uname");
        if(uname==null){
            log.info("*******用户名非法,请稍后再试!");
            exchange.getResponse().setStatusCode(HttpStatus.NOT_ACCEPTABLE);
            return exchange.getResponse().setComplete();

        }
        return chain.filter(exchange);
    }

//    @Override 实现上面@Order(0)注解就可以不用实现接口
//    public int getOrder() {
//        return 0;
//    }
}
123456789101112131415161718192021222324252627282930313233343536
```

**这里实现的是,请求进来必须带有uname的参数才可以访问,测试以下**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329114859761.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329114912359.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**可以写多个Filter进行过滤,传回过滤连就行chain**

### Gateway篇完,这篇后半部分有些没写是因为太多了,大家可以自己先尝试,方法都大同小异,点赞+关注不迷路

### 转载请标注~