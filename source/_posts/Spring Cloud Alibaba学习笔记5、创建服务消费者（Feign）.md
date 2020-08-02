---
title: Spring Cloud Alibaba学习笔记:5、创建服务消费者（Feign）
date: 2020-3-15
tags: Springcloud-Alibaba
categories: Springcloud-Alibaba
keywords: Spring
description: 面向服务
---

- # Spring Cloud Alibaba学习笔记:5、创建服务消费者（Feign）

- ### 文章目录

  - - - - [前言](https://blog.csdn.net/fxbin123/article/details/95029632#_3)

        - [Feign 概述](https://blog.csdn.net/fxbin123/article/details/95029632#Feign__8)

        - [创建feign 服务消费者](https://blog.csdn.net/fxbin123/article/details/95029632#feign__15)

        - - [1、 创建一个maven module 项目 `sca-nacos-discovery-consumer-feign`](https://blog.csdn.net/fxbin123/article/details/95029632#1_maven_module__scanacosdiscoveryconsumerfeign_17)

          - [2、 pom](https://blog.csdn.net/fxbin123/article/details/95029632#2_pom_18)

          - [3、创建应用主类别 `ScaNacosFeignApplication`](https://blog.csdn.net/fxbin123/article/details/95029632#3_ScaNacosFeignApplication_69)

          - [4、创建 feign 客户端](https://blog.csdn.net/fxbin123/article/details/95029632#4_feign__92)

          - [5、创建 `ConsumerController`](https://blog.csdn.net/fxbin123/article/details/95029632#5_ConsumerController_118)

          - [6、配置服务端口、名称、服务端点检查和Nacos地址](https://blog.csdn.net/fxbin123/article/details/95029632#6Nacos_153)

          - [7、启动Feign服务消费者](https://blog.csdn.net/fxbin123/article/details/95029632#7Feign_176)

          - [8、检查nacos 控制台](https://blog.csdn.net/fxbin123/article/details/95029632#8nacos__178)

          - [9、测试消费者](https://blog.csdn.net/fxbin123/article/details/95029632#9_180)

          - - [> 测试服务消费API](https://blog.csdn.net/fxbin123/article/details/95029632#_API_181)
            - [> 测试服务负载](https://blog.csdn.net/fxbin123/article/details/95029632#__188)

  

  #### 前言

  上篇文章， [Spring Cloud Alibaba学习笔记:4、创建服务消费者（RestTemplate + LoadBalancerClient）](https://blog.csdn.net/fxbin123/article/details/95025897)，
  我们一起学习了 使用 RestTemplate + LoadBalancerClient 的方式 去创建服务消费者，但是依旧显得繁琐，本文，我们将结合使用 spring-cloud-openfeign 来创建服务消费者，简化消费调用…
  一起开始吧

  #### Feign 概述

  > Feign 是一个声明式的伪 Http 客户端，它使得写 Http 客户端变得更简单。使用 Feign，只需要创建一个接口并注解。它具有可插拔的注解特性，可使用 Feign 注解和 JAX-RS 注解。Feign 支持可插拔的编码器和解码器。Feign 默认集成了 Ribbon，Nacos 也很好的兼容了 Feign，默认实现了负载均衡的效果

  - Feign 采用的是基于接口的注解
  - Feign 整合了 ribbon

  #### 创建feign 服务消费者

  ##### 1、 创建一个maven module 项目 `sca-nacos-discovery-consumer-feign`

  ##### 2、 pom

  ```
  <?xml version="1.0" encoding="UTF-8"?>
  <project xmlns="http://maven.apache.org/POM/4.0.0"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>
      <parent>
          <groupId>cn.fxbin.learn</groupId>
          <artifactId>sca</artifactId>
          <version>1.0</version>
      </parent>
  
      <artifactId>sca-nacos-discovery-consumer-feign</artifactId>
      <version>1.0</version>
      <name>sca-nacos-discovery-consumer-feign</name>
      <description>Spring Cloud Alibaba Nacos Discovery Consumer Feign Service Project</description>
  
      <dependencies>
          <!-- spring-boot web -->
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-web</artifactId>
          </dependency>
  
          <!-- nacos-discovery -->
          <dependency>
              <groupId>org.springframework.cloud</groupId>
              <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
          </dependency>
  
          <!-- openfeign -->
          <dependency>
              <groupId>org.springframework.cloud</groupId>
              <artifactId>spring-cloud-starter-openfeign</artifactId>
          </dependency>
      </dependencies>
  
      <build>
          <plugins>
              <plugin>
                  <groupId>org.springframework.boot</groupId>
                  <artifactId>spring-boot-maven-plugin</artifactId>
              </plugin>
          </plugins>
      </build>
  
  </project>
  12345678910111213141516171819202122232425262728293031323334353637383940414243444546
  ```

  ##### 3、创建应用主类别 `ScaNacosFeignApplication`

  ```
  package cn.fxbin.learn.sca.nacos.consumer;
  
  import org.springframework.boot.SpringApplication;
  import org.springframework.boot.autoconfigure.SpringBootApplication;
  import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
  import org.springframework.cloud.openfeign.EnableFeignClients;
  
  @EnableFeignClients
  @EnableDiscoveryClient
  @SpringBootApplication
  public class ScaNacosFeignApplication {
  
      public static void main(String[] args) {
          SpringApplication.run(ScaNacosFeignApplication.class, args);
      }
  
  }
  
  123456789101112131415161718
  ```

  ##### 4、创建 feign 客户端

  ```
  package cn.fxbin.learn.sca.nacos.consumer.client;
  
  import org.springframework.cloud.openfeign.FeignClient;
  import org.springframework.web.bind.annotation.GetMapping;
  import org.springframework.web.bind.annotation.PathVariable;
  
  /**
   * DiscoveryClient
   *
   * @author fxbin
   * @version v1.0
   * @since 2019/7/7 22:47
   */
  @FeignClient("sca-nacos-discovery-provider")
  public interface DiscoveryClient {
  
      @GetMapping(value = "/echo/{message}")
      String echo(@PathVariable String message);
  
  }
  
  123456789101112131415161718192021
  ```

  ##### 5、创建 `ConsumerController`

  ```
  package cn.fxbin.learn.sca.nacos.consumer.controller;
  
  import cn.fxbin.learn.sca.nacos.consumer.client.DiscoveryClient;
  import lombok.extern.slf4j.Slf4j;
  import org.springframework.web.bind.annotation.GetMapping;
  import org.springframework.web.bind.annotation.RestController;
  
  import javax.annotation.Resource;
  
  /**
   * ConsumerController
   *
   * @author fxbin
   * @version v1.0
   * @since 2019/7/7 20:36
   */
  @Slf4j
  @RestController
  public class ConsumerController {
  
      @Resource
      private DiscoveryClient discoveryClient;
  
      @GetMapping(value = "/test")
      public String test() {
          return discoveryClient.echo(" !^@^! ");
      }
  
  }
  
  123456789101112131415161718192021222324252627282930
  ```

  ##### 6、配置服务端口、名称、服务端点检查和Nacos地址

  ```
  server:
    port: 9033
  
  management:
    endpoints:
      web:
        exposure:
          include: "*"
  
  spring:
    application:
      name: sca-nacos-discovery-consumer-feign
    cloud:
      nacos:
        discovery:
          server-addr: 127.0.0.1:8848
  
  
  123456789101112131415161718
  ```

  ##### 7、启动Feign服务消费者

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190707225616949.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4YmluMTIz,size_16,color_FFFFFF,t_70)

  ##### 8、检查nacos 控制台

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190707225715357.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4YmluMTIz,size_16,color_FFFFFF,t_70)

  ##### 9、测试消费者

  ###### > 测试服务消费API

  postman 调用 api: http://localhost:9033/test

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190707225905774.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4YmluMTIz,size_16,color_FFFFFF,t_70)
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190707225945330.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4YmluMTIz,size_16,color_FFFFFF,t_70)

  ###### > 测试服务负载

  - 修改`sca-nacos-discovery-provider` 服务 api, 打印当前服务端口

  ```
    @RestController
    public class EchoController {
  
         @Value("${server.port}")
         private String port;
   
        @GetMapping(value = "/echo/{message}")
        public String echo(@PathVariable String message) {
            return "Hello Nacos Discovery Provider : " + message + ", port :" + port;
        }
    }
  1234567891011
  ```

  - 接下来重新启动原有实例，并再启动一个 `sca-nacos-discovery-provide` 的实例， 如下所示：
    ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190707230622801.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4YmluMTIz,size_16,color_FFFFFF,t_70)
  - 再次请求 http://localhost:9033/test , 会交替显示如下信息：

  ```
  Hello Nacos Discovery Provider :  !^@^! , port :8081
  Hello Nacos Discovery Provider :  !^@^! , port :9030
  12
  ```

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190707231059323.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4YmluMTIz,size_16,color_FFFFFF,t_70)
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190707231114625.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4YmluMTIz,size_16,color_FFFFFF,t_70)