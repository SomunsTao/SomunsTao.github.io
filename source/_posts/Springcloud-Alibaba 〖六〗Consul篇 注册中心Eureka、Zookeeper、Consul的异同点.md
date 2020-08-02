---
title: Springcloud-Alibaba 〖六〗Consul篇 注册中心Eureka、Zookeeper、Consul的异同点
date: 2020-04-06
tags: Springcloud-Alibaba
categories: Springcloud-Alibaba
keywords: spring
description: Springcloud-Alibaba
---



# Springcloud-Alibaba 〖六〗Consul篇 注册中心Eureka、Zookeeper、Consul的异同点

### Springcloud-Alibaba 〖六〗Consul篇

- - - [PS: github仓库[仓库地址](https://github.com/ktoking/springcloud-alibaba)项目都放到里面了](https://blog.csdn.net/kingtok/article/details/105036373#PS_githubhttpsgithubcomktokingspringcloudalibaba_1)

    - [一. 安装Consul(Windos版本)](https://blog.csdn.net/kingtok/article/details/105036373#_ConsulWindos_2)

    - - [1.1 下载并解压到文件夹](https://blog.csdn.net/kingtok/article/details/105036373#11__8)
      - [1.2 在路径下输入cmd进入命令行](https://blog.csdn.net/kingtok/article/details/105036373#12_cmd_10)
      - [1.3 在命令行输入consul,如果出现下面一堆东西,则安装成功](https://blog.csdn.net/kingtok/article/details/105036373#13_consul_12)
      - [1.4 输入 consul agent -dev 启动 Consul](https://blog.csdn.net/kingtok/article/details/105036373#14__consul_agent_dev__Consul_14)

    - [二. 新建支付服务module cloud-providerconsul-payment8006](https://blog.csdn.net/kingtok/article/details/105036373#_module_cloudproviderconsulpayment8006_19)

    - - [2.1 模块8006结构](https://blog.csdn.net/kingtok/article/details/105036373#21_8006_20)
      - [2.2 改pom](https://blog.csdn.net/kingtok/article/details/105036373#22_pom_22)
      - [2.3 建 application.yml 配置文件](https://blog.csdn.net/kingtok/article/details/105036373#23__applicationyml__72)
      - [2.4 主启动类](https://blog.csdn.net/kingtok/article/details/105036373#24__91)
      - [2.5 业务类Controller层](https://blog.csdn.net/kingtok/article/details/105036373#25_Controller_110)
      - [2.6 测试](https://blog.csdn.net/kingtok/article/details/105036373#26__136)

    - [三. 新建modul cloud-consumerconsul-order80](https://blog.csdn.net/kingtok/article/details/105036373#_modul_cloudconsumerconsulorder80_146)

    - - [3.1 目录结构](https://blog.csdn.net/kingtok/article/details/105036373#31__147)
      - [3.2 改pom](https://blog.csdn.net/kingtok/article/details/105036373#32_pom_149)
      - [3.2 建 application.yml 配置文件](https://blog.csdn.net/kingtok/article/details/105036373#32__applicationyml__201)
      - [3.3 主启动类](https://blog.csdn.net/kingtok/article/details/105036373#33__220)
      - [3.4 业务类Controller层](https://blog.csdn.net/kingtok/article/details/105036373#34_Controller_240)
      - [3.5 配置Bean RestTemplate模板](https://blog.csdn.net/kingtok/article/details/105036373#35_Bean_RestTemplate_267)
      - [3.6 测试启动](https://blog.csdn.net/kingtok/article/details/105036373#36__287)

    - [四. 注册中心Eureka、Zookeeper、Consul的异同点](https://blog.csdn.net/kingtok/article/details/105036373#_EurekaZookeeperConsul_291)

    - [Consul篇完,下一章是Ribbon和OpenFeign~ 点赞加关注,不迷路~](https://blog.csdn.net/kingtok/article/details/105036373#ConsulRibbonOpenFeign__306)



### PS: github仓库[仓库地址](https://github.com/ktoking/springcloud-alibaba)项目都放到里面了

### 一. 安装Consul(Windos版本)

**官网下载太慢了,我上传到百度云了**

[百度云链接](https://pan.baidu.com/s/18hH7WQwivpUG1tEj7ckC7Q)
**提取码: fs72**

#### 1.1 下载并解压到文件夹

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200322213818877.png)

#### 1.2 在路径下输入cmd进入命令行

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200322213902985.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 1.3 在命令行输入consul,如果出现下面一堆东西,则安装成功

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200322213948948.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 1.4 输入 consul agent -dev 启动 Consul

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200322214934709.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**在我们本地8500端口可以看见Consul自带的UI界面**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200322215012264.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

### 二. 新建支付服务module cloud-providerconsul-payment8006

#### 2.1 模块8006结构

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200323100439692.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 2.2 改pom

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

    <artifactId>cloud-providerconsul-payment8006</artifactId>

    <dependencies>
        <!--springcloud consul-server-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-consul-discovery</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--日常通用jar包-->
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
12345678910111213141516171819202122232425262728293031323334353637383940414243444546
```

#### 2.3 建 application.yml 配置文件

```yml
server:
  port: 8006

spring:
  application:
    name: consul-provider-payment
  ###consul注册中心地址
  cloud:
    consul:
      host: localhost
      port: 8500
      discovery:
        #hostname: 127.0.0.1
        service-name: ${spring.application.name}
1234567891011121314
```

#### 2.4 主启动类

```java
package com.atguigu.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
@EnableDiscoveryClient
public class PaymentMain8006 {
    public static void main(String[] args) {
        SpringApplication.run(PaymentMain8006.class,args);
    }
}
1234567891011121314
```

#### 2.5 业务类Controller层

```java
package com.atguigu.springcloud.controller;

import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.UUID;

@RestController
@Slf4j
public class PaymentConsulController {

    @Value("${server.port}")
    private String serverPort;

    @GetMapping(value = "/payment/consul")
    public String paymentConsul(){
        return "Spring cloud with consul port: "+serverPort+"\t  "+ UUID.randomUUID().toString();
    }
}
123456789101112131415161718192021
```

#### 2.6 测试

**打开 consul UI界面可以看到我们的微服务已经注册进来**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200323110212669.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**在conusl里面可以看到其他的一些数据**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200323110238397.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

**测试业务**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200323100814594.png)

### 三. 新建modul cloud-consumerconsul-order80

#### 3.1 目录结构

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200323105526626.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

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

    <artifactId>cloud-consumerconsul-order80</artifactId>


    <dependencies>
        <!--springcloud consul-server-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-consul-discovery</artifactId>
        </dependency>
        <!--springboot整合web组件-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--日常通用jar包配置-->
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
        </dependency>
    </dependencies>
</project>

123456789101112131415161718192021222324252627282930313233343536373839404142434445464748
```

#### 3.2 建 application.yml 配置文件

```yml
### consul服务端口号
server:
  port: 80

spring:
  application:
    name: cloud-consumer-order
  ###consul服务注册中心
  cloud:
    consul:
      host: localhost
      port: 8500
      discovery:
        #hostname: 127.0.0.1
        service-name: ${spring.application.name}
123456789101112131415
```

#### 3.3 主启动类

```java
package com.atguigu.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

import javax.swing.*;

@SpringBootApplication
@EnableDiscoveryClient
public class OrderConsulMain80 {
    public static void main(String[] args) {
        SpringApplication.run(OrderConsulMain80.class,args);
    }
}

12345678910111213141516
```

#### 3.4 业务类Controller层

```java
package com.atguigu.springcloud.controller;

import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

import javax.annotation.Resource;

@RestController
@Slf4j
public class OrderConsulController {
    public static final String INVOKE_URL ="http://consul-provider-payment";

    @Resource
    private RestTemplate restTemplate;

    @GetMapping(value = "/consumer/payment/consul")
    public String getPaymentInfo(){
        return restTemplate.getForObject(INVOKE_URL+"/payment/consul",String.class);
    }
}

1234567891011121314151617181920212223
```

#### 3.5 配置Bean RestTemplate模板

```java
package com.atguigu.springcloud.config;

import org.springframework.cloud.client.loadbalancer.LoadBalanced;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.client.RestTemplate;

@Configuration
public class ApplicationContextConfig {

    @Bean
    @LoadBalanced
    public RestTemplate getRestTemplate(){
        return new RestTemplate();
    }
}
12345678910111213141516
```

#### 3.6 测试启动

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200323110305271.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**业务类测试**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200323110326911.png)

### 四. 注册中心Eureka、Zookeeper、Consul的异同点

| 组件名    | 语言 | CAP  | 服务监控检查 | 对外暴露接口 | Springcloud集成 |
| --------- | ---- | ---- | ------------ | ------------ | --------------- |
| Eureka    | Java | AP   | 可配支持     | HTTP         | 已集成          |
| Consul    | Go   | CP   | 支持         | HTTP/DNS     | 已集成          |
| Zookeeper | Java | CP   | 支持         | 客户端       | 已集成          |

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200323114306854.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**最多同时满足两个**

- **C：Consistency （强一致性）**
- **A：Available （可用性）**
- **P：Partition tolerance （分区容错性）**

### Consul篇完,下一章是Ribbon和OpenFeign~ 点赞加关注,不迷路~