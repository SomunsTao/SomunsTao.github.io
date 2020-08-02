---
title: Springcloud-Alibaba 〖九〗Hystrix篇 服务降级,服务熔断,服务限流,图形化界面
date: 2020-03-09
tags: Springcloud-Alibaba
categories: Springcloud-Alibaba
keywords: spring
description: Springcloud-Alibaba
---



# Springcloud-Alibaba 〖九〗Hystrix篇 服务降级,服务熔断,服务限流,图形化界面

### Springcloud-Alibaba 〖九〗Hystrix篇

- - - [PS: github仓库[仓库地址](https://github.com/ktoking/springcloud-alibaba)项目都放到里面了](https://blog.csdn.net/kingtok/article/details/105104811#PS_githubhttpsgithubcomktokingspringcloudalibaba_1)

    - [一. 分布式系统面临的问题](https://blog.csdn.net/kingtok/article/details/105104811#__2)

    - - [服务雪崩](https://blog.csdn.net/kingtok/article/details/105104811#_5)

    - [二. Hystrix是什么?](https://blog.csdn.net/kingtok/article/details/105104811#_Hystrix_13)

    - [三. 新建模块module cloud-provider-hystrix-payment8001](https://blog.csdn.net/kingtok/article/details/105104811#_module_cloudproviderhystrixpayment8001_23)

    - - [3.1 目录结构](https://blog.csdn.net/kingtok/article/details/105104811#31__24)
      - [3.2 改pom](https://blog.csdn.net/kingtok/article/details/105104811#32_pom_26)
      - [3.3 建 application.yml](https://blog.csdn.net/kingtok/article/details/105104811#33__applicationyml_88)
      - [3.4 主启动类](https://blog.csdn.net/kingtok/article/details/105104811#34__108)
      - [3.5 业务类](https://blog.csdn.net/kingtok/article/details/105104811#35__127)
      - [3.6 测试](https://blog.csdn.net/kingtok/article/details/105104811#36__197)

    - [四. 压力测试](https://blog.csdn.net/kingtok/article/details/105104811#__202)

    - [五. 新建cloud-consumer-feign-hystrix-order80服务](https://blog.csdn.net/kingtok/article/details/105104811#_cloudconsumerfeignhystrixorder80_217)

    - - [5.1 项目目录](https://blog.csdn.net/kingtok/article/details/105104811#51__218)
      - [5.2 pom](https://blog.csdn.net/kingtok/article/details/105104811#52_pom_221)
      - [5.3 application.yml](https://blog.csdn.net/kingtok/article/details/105104811#53_applicationyml_287)
      - [5.4 主启动](https://blog.csdn.net/kingtok/article/details/105104811#54__299)
      - [5.5 业务类](https://blog.csdn.net/kingtok/article/details/105104811#55__320)

    - [六. 服务降级(8001项目)](https://blog.csdn.net/kingtok/article/details/105104811#_8001_379)

    - - [6.1 主启动开启Hystrix](https://blog.csdn.net/kingtok/article/details/105104811#61_Hystrix_380)
      - [6.2 添加兜底方法](https://blog.csdn.net/kingtok/article/details/105104811#62___382)
      - [6.3 全局服务降级@DefaultProperties](https://blog.csdn.net/kingtok/article/details/105104811#63_DefaultProperties_406)
      - [6.4 客户端的服务降级](https://blog.csdn.net/kingtok/article/details/105104811#64__424)

    - [七. 服务熔断](https://blog.csdn.net/kingtok/article/details/105104811#__452)

    - - [7.1 服务熔断8001服务](https://blog.csdn.net/kingtok/article/details/105104811#71_8001_455)
      - [7.2 controller服务](https://blog.csdn.net/kingtok/article/details/105104811#72_controller_478)
      - [7.3 测试](https://blog.csdn.net/kingtok/article/details/105104811#73__489)
      - [7.4 总结](https://blog.csdn.net/kingtok/article/details/105104811#74__495)

    - [八. 服务限流](https://blog.csdn.net/kingtok/article/details/105104811#__520)

    - [九. Hystrix DashBoard 图形化界面搭建module cloud-consumer-hystrix-dashboard9001](https://blog.csdn.net/kingtok/article/details/105104811#_Hystrix_DashBoard_module_cloudconsumerhystrixdashboard9001_523)

    - - [9.1 工程目录](https://blog.csdn.net/kingtok/article/details/105104811#91__527)

      - [9.2 pom](https://blog.csdn.net/kingtok/article/details/105104811#92_pom_529)

      - [9.3 application.yml](https://blog.csdn.net/kingtok/article/details/105104811#93_applicationyml_575)

      - [9.4 主启动](https://blog.csdn.net/kingtok/article/details/105104811#94__581)

      - [9.5 测试](https://blog.csdn.net/kingtok/article/details/105104811#95__599)

      - - [9.5.1 PaymentHystrixMain8001 添加代码](https://blog.csdn.net/kingtok/article/details/105104811#951_PaymentHystrixMain8001__602)
        - [9.5.2 开启三个服务](https://blog.csdn.net/kingtok/article/details/105104811#952__646)
        - [9.5.3 输入如下点击监控](https://blog.csdn.net/kingtok/article/details/105104811#953__649)
        - [9.5.4 查看监控平台](https://blog.csdn.net/kingtok/article/details/105104811#954__652)
        - [9.5.5 怎么看监控平台](https://blog.csdn.net/kingtok/article/details/105104811#955__660)

    - [Hystrix篇完,可以把精力放在后面跟他更重要的Sentine上~关注点赞不迷路](https://blog.csdn.net/kingtok/article/details/105104811#HystrixSentine_671)

    - [转载要标注~](https://blog.csdn.net/kingtok/article/details/105104811#_672)



### PS: github仓库[仓库地址](https://github.com/ktoking/springcloud-alibaba)项目都放到里面了

### 一. 分布式系统面临的问题

复杂分布式体系结构中的应用程序有数十个依赖关系，每个依赖关系在某些时候将不可避免地损失的。

#### 服务雪崩

多个微服务之间调用的时候，假设微服务A调用微服务B和微服务C,微服务B和微服务C又调用其它的微服务，这就是所谓的"**扇出**"。
如果扇出的链路上某个微服务的调用响应时间过长或者不可用,对微服务A的调用就会占用越来越多的系统资源，进而引起系统崩溃，所谓的“**雪崩效应**”.

对于高流量的应用来说，单-的后端依赖可能会导致所有服务器上的所有资源都在几秒钟内饱和。比失败更糟糕的是，**这些应用程序还可能导致服务之间的延迟增加，备份队列，线程和其他系统资源紧张，导致整个系统发生更多的级联故障**。这些都表示需要对故障和延迟进行隔离和管理，以便单个依赖关系的失败,不能取消整个应用程序或系统。

**所以,通常当你发现一个模块下的某个实例失败后,这时候这个模块依然还会接收流量,然后这个有问题的模块还调用了其他的模块,这样就会发生级联故障,或者叫雪崩。**

### 二. Hystrix是什么?

Hystrix是一个用于**处理分布式系统的延迟和容错的开源库**, 在分布式系统里,许多依赖不可避免的会调用失败，比如超时、异常等

Hystrix能够保证在一个依赖出问题的情况下，**不会导致整体服务失败，避免级联故障,以提高分布式系统的弹性**。

“**断路器**”本身是一种开关装置,当某个服务单元发生故障之后，通过断路器的故障监控(类似熔断保险丝)，**向调用方返回一个符合预期的、可处理的备选响应(FallBack) ，而不是长时间的等待或者抛出调用方无法处理的异常**，这样就保证了服务调用方的线程不会被长时间、不必要地占用,从而避免了故障在分布式系统中的蔓延,乃至雪崩。

**但是停更了…,不影响先学一波,毕竟设计理论与思想最重要嘛,大家都是抄作业,像阿里的sentinel…会不会我到时候也自研一个哈哈~**

### 三. 新建模块module cloud-provider-hystrix-payment8001

#### 3.1 目录结构

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020032611352681.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

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

    <artifactId>cloud-provider-hystrix-payment8001</artifactId>

    <dependencies>
        <!--hystrix-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
        </dependency>
        <!--eureka client-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
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
        <!--引入自定义的api通用包，可用使用Payment支付Entity-->
        <dependency>
            <groupId>com.atguigu.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>${project.version}</version>
        </dependency>
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

12345678910111213141516171819202122232425262728293031323334353637383940414243444546474849505152535455565758
```

#### 3.3 建 application.yml

```yml
server:
  port: 8001

spring:
  application:
    name: cloud-provider-hystrix-payment

eureka:
  client:
    #表示是否将自己注册进EurekaServer默认为true
    register-with-eureka: true
    #是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
    fetch-registry: true
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka
#      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/
12345678910111213141516
```

#### 3.4 主启动类

```java
package com.atguigu.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

@EnableDiscoveryClient
@SpringBootApplication
public class PaymentHystrixMain8001 {
    public static void main(String[] args) {
        SpringApplication.run(PaymentHystrixMain8001.class,args);
    }
}

1234567891011121314
```

#### 3.5 业务类

**service层**.

```java
package com.atguigu.springcloud.service;

import org.springframework.stereotype.Service;

import java.util.concurrent.TimeUnit;

@Service
public class PaymentService {

    public String paymentInfoOK(Integer id){
        return "当前线程: "+Thread.currentThread().getName()+"paymentInfo_OK,id："+id+"\t"+"O(∩_∩)O哈哈~";
    }

    public String paymentInfoTimeOut(Integer id){
        int timeout=3;
        try {
            TimeUnit.SECONDS.sleep(timeout);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return "线程池："+Thread.currentThread().getName()+"   paymentInfo_Timeout,id："+id+"\t"+"O(∩_∩)O哈哈~"+"   耗时(秒)："+timeout;
    }
}

123456789101112131415161718192021222324
```

**controller层**

```java
package com.atguigu.springcloud.controller;

import com.atguigu.springcloud.service.PaymentService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;

@RestController
@Slf4j
public class PaymentHystrixController {

    @Resource
    private PaymentService paymentService;
    @Value("${server.port}")
    private String serverPort;

    @GetMapping(value = "/payment/hystrix/ok/{id}")
    public String paymentInfoOK(@PathVariable("id") Integer id){
        String result = paymentService.paymentInfoOK(id);
        log.info("*****result："+result);
        return result;
    }

    @GetMapping(value = "/payment/hystrix/timeout/{id}")
    public String paymentInfo_TimeOut(@PathVariable("id") Integer id){
        String result = paymentService.paymentInfoTimeOut(id);
        log.info("*****result："+result);
        return result;
    }


}

12345678910111213141516171819202122232425262728293031323334353637
```

#### 3.6 测试

**正常的方法**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200326114512119.png)
**有延时的方法**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200326114522695.png)

### 四. 压力测试

**安装JMeter**
[官网安装链接](http://jmeter.apache.org/download_jmeter.cgi)

[百度网盘下载](https://pan.baidu.com/s/1TK8hDLEj7h0gYGrm3iWfKg)
提取码：ibc9

**去到Bin目录下,打开如图,即可启动JMeter**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200326145422369.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200326150610212.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**启动后,发现不只是超时的服务卡,就连之前ok的服务也会卡的2-3秒**

### 五. 新建cloud-consumer-feign-hystrix-order80服务

#### 5.1 项目目录

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200326162932972.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

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

    <artifactId>cloud-consumer-feign-hystrix-order80</artifactId>

    <dependencies>
        <!--    openfeign    -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
        <!--   hystrix     -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
        </dependency>
        <!--   eureka  client    -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <!--   自定义的api通用包     -->
        <dependency>
            <groupId>com.atguigu.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
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
1234567891011121314151617181920212223242526272829303132333435363738394041424344454647484950515253545556575859606162
```

#### 5.3 application.yml

```yml
server:
  port: 80
eureka:
  client:
    fetch-registry: false
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka

12345678
```

#### 5.4 主启动

```java
package com.atguigu.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.circuitbreaker.EnableCircuitBreaker;
import org.springframework.cloud.netflix.hystrix.EnableHystrix;
import org.springframework.cloud.openfeign.EnableFeignClients;

@SpringBootApplication
@EnableFeignClients
@EnableHystrix
public class OrderHystrixMain80 {
    public static void main(String[] args) {
        SpringApplication.run(OrderHystrixMain80.class, args);
    }
}
12345678910111213141516
```

#### 5.5 业务类

**service**

```java
package com.atguigu.springcloud.service;

import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.stereotype.Component;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;

@Component
@FeignClient(value = "CLOUD-PROVIDER-HYSTRIX-PAYMENT")
public interface PaymentHystrixService {

    @GetMapping("/payment/hystrix/ok/{id}")
    String paymentInfoOK(@PathVariable("id") Integer id);

    @GetMapping("/payment/hystrix/timeout/{id}")
    String paymentInfoTimeOut(@PathVariable("id") Integer id);

}
123456789101112131415161718
```

**controller**

```java
package com.atguigu.springcloud.controller;

import com.atguigu.springcloud.service.PaymentHystrixService;

import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;

@RestController
@Slf4j
public class OrderHystrixController {
    
    @Resource
    private PaymentHystrixService paymentHystrixService;
    
    @GetMapping(value ="/consumer/payment/hystrix/ok/{id}")
    public String paymentInfoOK(@PathVariable("id") Integer id){
        String result = paymentHystrixService.paymentInfoOK(id);
        return  result;
    }

    @GetMapping(value ="/consumer/payment/hystrix/timeout/{id}")
    public String paymentInfo_TimeOut(@PathVariable("id") Integer id){
        String result = paymentHystrixService.paymentInfoTimeOut(id);
        return result;
    }
}

1234567891011121314151617181920212223242526272829303132
```

### 六. 服务降级(8001项目)

#### 6.1 主启动开启Hystrix

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200326173044204.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 6.2 添加兜底方法

**fallbackMethod方法加到可能出错的服务上,再多写一个兜底的方法,只要访问时长超过3s,或服务异常,直接调用我们的兜底方法**

```java
    @HystrixCommand(fallbackMethod = "paymentInfoTimeOutHandler",commandProperties = {
            @HystrixProperty(name = "execution.isolation.thread.timeoutInMilliseconds",value = "3000")
    })
    public String paymentInfoTimeOut(Integer id){
        int timeout=5;
        try {
            TimeUnit.SECONDS.sleep(timeout);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return "线程池："+Thread.currentThread().getName()+"   paymentInfo_Timeout,id："+id+"\t"+"O(∩_∩)O哈哈~"+"   耗时(秒)："+timeout;
    }

    public String  paymentInfoTimeOutHandler(Integer id){
        return "程序运行繁忙或报错,请稍后再试*****"+"当前线程: "+Thread.currentThread().getName()+id+"\t "+"orz!";
    }
}
1234567891011121314151617
```

**测试**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200326173256428.png)

#### 6.3 全局服务降级@DefaultProperties

**我们不可能每写一个方法就写一个fallback方法,我们可以同意的处理这些fallback方法**

**指定全局降级的方法**.
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200326200723145.png)
**全局降级方法实现**

```java
 public String payment_Global_FallbackMethod(){
        return "Global异常处理信息,请稍后再试: orz~";
    }
123
```

**调用规则**

- **如果加了之前的详细配置规则,那就还是按照之前的规则来进行,比如设置最大不超过1秒.需要哪个方法去有全局的降级规则,就单一的添加@HystrixCommand注解**
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/2020032620090253.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
  **测试**
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200326201045829.png)

#### 6.4 客户端的服务降级

**建立Service接口的实现类即可,这样既解耦而且代码量处理的很少,不用在一个controller里面进行大量的代码堆积,解决了代码膨胀与混乱**

```java
package com.atguigu.springcloud.service;

import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.stereotype.Component;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;

@Component
@FeignClient(value = "cloud-provider-hystrix-payment",fallback = PaymentHystrixServiceImpl.class)
public interface PaymentHystrixService {

    @GetMapping("/payment/hystrix/ok/{id}")
    String paymentInfoOK(@PathVariable("id") Integer id);

    @GetMapping("/payment/hystrix/timeout/{id}")
    String paymentInfoTimeOut(@PathVariable("id") Integer id);

}
123456789101112131415161718
```

**实现其中的方法,这样即可在每个单的的方法中去实现fallback方法**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200327163338636.png)
**让客户端在服务端不可用时也会获得提示信息而不会挂起耗死服务器**

### 七. 服务熔断

**微服务架构大神博客:** [博客链接](https://martinfowler.com/bliki/CircuitBreaker.html)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200327174100705.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 7.1 服务熔断8001服务

**来到8001service,添加方法**

```java
 @HystrixCommand(fallbackMethod="paymentCircuitBreakerFallback", commandProperties={
            @HystrixProperty(name = "circuitBreaker.enabled" ,value = "true"),//是否开启断路器
            @HystrixProperty(name = "circuitBreaker.requestVolumeThreshold",value = "10"),// 请求次数
            @HystrixProperty(name = "circuitBreaker.sleepWindowInMilliseconds",value = "10000"),//时间窗口期 "
            @HystrixProperty(name = "circuitBreaker.errorThresholdPercentage",value = "60")//失败率达到多少后跳闸
            })
    public String paymentCircuitBreaker(@PathVariable("id") Integer id){
        if (id<0){
            throw new RuntimeException();
        }
        String serialNumber = IdUtil.simpleUUID();
        return Thread.currentThread().getName()+"\t "+"调用成功,流水号: "+serialNumber;
    }
    public String paymentCircuitBreakerFallback(@PathVariable("id") Integer id){
        return "id不能为负数,请稍后再试~ id: "+ id;
    }
12345678910111213141516
```

**可以去HystrixCommandProperties类里面去查看配置的注解参数名**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200327204551388.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 7.2 controller服务

```java
//    ***************服务熔断************************
    @GetMapping("/payment/circuit/{id}")
    public String paymentCircuitBreaker(@PathVariable("id") Integer id){
        String circuitBreaker = paymentService.paymentCircuitBreaker(id);
        log.info("******result: "+circuitBreaker);
        return circuitBreaker;
    }
1234567
```

#### 7.3 测试

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200327211034653.png)**正确的值测试成功**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200327211133396.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200327211152798.png)
**当我们调用失败的次数达到我们设置的预期值,我们>0的值也开始服务报错,等一段时间后,服务又可以正常使用了**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200327211341601.png)

#### 7.4 总结

- **熔断打开**
  请求不再进行调用当前服务，内部设置时钟一般为MTTR (平均故障处理时间)，当打开时长达到所设时钟则进入半熔断状态
- **熔断关闭**
  熔断关闭不会对服务进行熔断
- **熔断半开**
  部分请求根据规则调用当前服务，如果请求成功且符合规则则认为当前服务恢复正常，关闭熔断

**设计熔断的三个参数**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200327212123725.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**就是这哥三~**

涉汲到断路器的三个重要参数:**快照时间窗**、**请求总数阀值**、**错误百分比阀值。**

- **1:快照时间窗:断路器确定是否打开需要统计一些请求和错误数据，而统计的时间范围就是快照时间窗,默认为最近的10秒。**
- **2:请求总数阀值:在快照时间窗内，必须满足请求总数阀值才有资格熔断。默认为20, 意味着在10秒内,如果该hystrix命令的调用次数不足20次,即使所有的请求都超时或其他原因失败，断路器都不会打开。**
- **3:错误百分比阀值:当请求总数在快照时间窗内超过了阀值，比如发生了30次调用，如果在这30次调用中，有15次发生了超时异常,也就是超过50%的错误百分比，在默认设定50%阀值情况下，这时候就会将断路器打开。**

**原来的主逻辑要如何恢复呢?**

对于这一-问题，hystrix也为我们实现了**自动恢复功能**。

- 当断路器打开,对主逻辑进行熔断之后，hystrix会启动一个**休眠时间窗**在这个时间窗内,降级逻辑是临时的成为主逻辑，
- 当休眠时间窗到期，断路器将进入半开状态,释放一次请求到原来的主逻辑上,如果此次请求正常返回,那么断路器将继续闭合,
- 主逻辑恢复,如果这次请求依然有问题，断路器继续进入打开状态,休眠时间窗重新计时。

### 八. 服务限流

**这里不多BB,之后带你们去看alibaba的Sentineli,溜了溜了~**

### 九. Hystrix DashBoard 图形化界面搭建module cloud-consumer-hystrix-dashboard9001

**但凡是监控,这两个一定要有**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328111957734.png)

#### 9.1 工程目录

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328110601938.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 9.2 pom

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

    <artifactId>cloud-consumer-hystrix-dashboard9001</artifactId>


    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-hystrix-dashboard</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
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

123456789101112131415161718192021222324252627282930313233343536373839404142
```

#### 9.3 application.yml

```yml
server:
  port: 9001
12
```

#### 9.4 主启动

```java
package com.atguigu.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.hystrix.dashboard.EnableHystrixDashboard;

@SpringBootApplication
@EnableHystrixDashboard
public class HystrixDashboardMain9001 {
    public static void main(String[] args) {
        SpringApplication.run(HystrixDashboardMain9001.class,args);
    }
}

1234567891011121314
```

#### 9.5 测试

**打开http://localhost:9001/hystrix就能看到监控平台**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200327214849889.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

##### 9.5.1 PaymentHystrixMain8001 添加代码

```java
package com.atguigu.springcloud;

import com.netflix.hystrix.contrib.metrics.eventstream.HystrixMetricsStreamServlet;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.web.servlet.ServletRegistrationBean;
import org.springframework.cloud.client.circuitbreaker.EnableCircuitBreaker;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
import org.springframework.cloud.netflix.hystrix.EnableHystrix;
import org.springframework.context.annotation.Bean;

@EnableEurekaClient
@SpringBootApplication
@EnableCircuitBreaker
public class PaymentHystrixMain8001 {
    public static void main(String[] args) {
        SpringApplication.run(PaymentHystrixMain8001.class,args);
    }


    //主启动类添加代码
    /**
     * 此配置是为了服务监控而配置，与服务容错本身无关,SpringCloud升级后的坑
     * ServletRegistrationBean因为springboot的默认路径不是"/hystrix.stream"，
     * 只要在自己的项目里配置上下面的servlet就可以了
     */
    @Bean
    public ServletRegistrationBean getServlet(){
        HystrixMetricsStreamServlet streamServlet = new HystrixMetricsStreamServlet();
        ServletRegistrationBean registrationBean = new ServletRegistrationBean(streamServlet);
        registrationBean.setLoadOnStartup(1);
        registrationBean.addUrlMappings("/hystrix.stream");
        registrationBean.setName("HystrixMetricsStreamServlet");
        return registrationBean;
    }

}

123456789101112131415161718192021222324252627282930313233343536373839
```

##### 9.5.2 开启三个服务

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328112732579.png)

##### 9.5.3 输入如下点击监控

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328113055475.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

##### 9.5.4 查看监控平台

**我们一直访问8001项目之前正确的服务,在监控平台上就会出现对服务的数据监控**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328113144911.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**当我们尝试触发熔断机制,疯狂访问抛异常的服务时,断路器就会打开**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328113351438.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**过一段时间后,断路器会自动关闭**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328113453672.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

##### 9.5.5 怎么看监控平台

**七种颜色对应不同的事件**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328113641242.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**实心圆**:共有两种含义。它通过颜色的变化代表了实例的健康程度,它的健康度从**绿色<黄色<橙色<红色递减**。

该实心圆除了颜色的变化之外,它的大小也会根据实例的请求流量发生变化,**流量越大该实心圆就越大**。所以通过该实心圆的展示，就可以在大量的实例中快速的发现故障实例和高压力实例。

**一条曲线可以观察到流量的上升与下降的趋势**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200328113954231.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

### Hystrix篇完,可以把精力放在后面跟他更重要的Sentine上~关注点赞不迷路

### 转载要标注~