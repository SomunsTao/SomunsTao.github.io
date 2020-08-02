---
title: Spring Cloud Alibaba学习笔记:4、创建服务消费者
date: 2020-3-14
tags: Springcloud-Alibaba
categories: Springcloud-Alibaba
keywords: Spring
description: 面向服务
---

- # Spring Cloud Alibaba学习笔记:4、创建服务消费者（RestTemplate + LoadBalancerClient）

  ### 文章目录

  - - - [前言](https://blog.csdn.net/fxbin123/article/details/95025897#_2)

      - [创建 Nacos RestTemplate 消费者](https://blog.csdn.net/fxbin123/article/details/95025897#_Nacos__RestTemplate__5)

      - - [1、 创建一个maven module 项目 `sca-nacos-discovery-consumer-resttemplate`](https://blog.csdn.net/fxbin123/article/details/95025897#1_maven_module__scanacosdiscoveryconsumerresttemplate_7)
        - [2、 pom.xml 配置](https://blog.csdn.net/fxbin123/article/details/95025897#2_pomxml__8)
        - [3、创建应用主类别 `ScaNacosConsumerApplication`](https://blog.csdn.net/fxbin123/article/details/95025897#3_ScaNacosConsumerApplication_52)
        - [4、创建 `RestTemplateConfig`](https://blog.csdn.net/fxbin123/article/details/95025897#4_RestTemplateConfig_71)
        - [5、创建 `ConsumerController`](https://blog.csdn.net/fxbin123/article/details/95025897#5_ConsumerController_99)
        - [6、配置服务端口、名称、服务端点检查和Nacos地址](https://blog.csdn.net/fxbin123/article/details/95025897#6Nacos_136)
        - [7、启动服务消费者](https://blog.csdn.net/fxbin123/article/details/95025897#7_157)
        - [8、检查nacos 控制台](https://blog.csdn.net/fxbin123/article/details/95025897#8nacos__161)
        - [9、测试消费者](https://blog.csdn.net/fxbin123/article/details/95025897#9_165)
        - - - [> 服务端点检查](https://blog.csdn.net/fxbin123/article/details/95025897#___167)
            - [> 测试服务消费API](https://blog.csdn.net/fxbin123/article/details/95025897#__API_172)

  

  ### 前言

  上篇文章 [Spring Cloud Alibaba学习笔记: 3、Nacos - 服务注册与发现](https://blog.csdn.net/fxbin123/article/details/94918724) 中，我们创建了实现了通过 Nacos 的服务提供者与发现者，也通过使用 `LoadBalancerClient` 来获取某个服务实例，并消费服务接口，但是异常繁琐，不是很友好，本片文正是对上文 服务消费者有优化实现， 絮絮叨叨了半天，接下来开启新的探索吧…

  ### 创建 Nacos RestTemplate 消费者

  #### 1、 创建一个maven module 项目 `sca-nacos-discovery-consumer-resttemplate`

  #### 2、 pom.xml 配置

  ```
  <?xml version="1.0" encoding="UTF-8"?>
  <project xmlns="http://maven.apache.org/POM/4.0.0"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <parent>
          <artifactId>sca</artifactId>
          <groupId>cn.fxbin.learn</groupId>
          <version>1.0</version>
      </parent>
      <modelVersion>4.0.0</modelVersion>
  
      <artifactId>sca-nacos-discovery-consumer-resttemplate</artifactId>
      <version>1.0</version>
      <name>sca-nacos-discovery-consumer-resttemplate</name>
      <description>Spring Cloud Alibaba Nacos Discovery Consumer RestTemplate Service Project</description>
  
      <dependencies>
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-web</artifactId>
          </dependency>
  
          <dependency>
              <groupId>org.springframework.cloud</groupId>
              <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
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
  123456789101112131415161718192021222324252627282930313233343536373839
  ```

  #### 3、创建应用主类别 `ScaNacosConsumerApplication`

  ```
  package cn.fxbin.learn.sca.nacos.consumer;
  
  import org.springframework.boot.SpringApplication;
  import org.springframework.boot.autoconfigure.SpringBootApplication;
  import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
  
  
  @EnableDiscoveryClient
  @SpringBootApplication
  public class ScaNacosConsumerApplication {
  
      public static void main(String[] args) {
          SpringApplication.run(ScaNacosConsumerApplication.class, args);
      }
  }
  123456789101112131415
  ```

  #### 4、创建 `RestTemplateConfig`

  ```
  package cn.fxbin.learn.sca.nacos.consumer.config;
  
  import org.springframework.cloud.client.loadbalancer.LoadBalanced;
  import org.springframework.context.annotation.Bean;
  import org.springframework.context.annotation.Configuration;
  import org.springframework.web.client.RestTemplate;
  
  /**
   * RestTemplateConfig
   *
   * @author fxbin
   * @version v1.0
   * @since 2019/7/7 20:37
   */
  @Configuration
  public class RestTemplateConfig {
  
      @Bean
      @LoadBalanced
      public RestTemplate restTemplate(){
          return new RestTemplate();
      }
  
  }
  123456789101112131415161718192021222324
  ```

  #### 5、创建 `ConsumerController`

  ```
  package cn.fxbin.learn.sca.nacos.consumer.controller;
  
  import lombok.extern.slf4j.Slf4j;
  import org.springframework.web.bind.annotation.GetMapping;
  import org.springframework.web.bind.annotation.RestController;
  import org.springframework.web.client.RestTemplate;
  
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
      private RestTemplate restTemplate;
  
      @GetMapping(value = "/test")
      public String test() {
  
          String url = String.format("http://%s/echo/%s", "sca-nacos-discovery-provider", " !^@^! ");
          log.info("请求路径为：{}", url);
          return restTemplate.getForObject(url, String.class);
      }
  
  }
  1234567891011121314151617181920212223242526272829303132
  ```

  #### 6、配置服务端口、名称、服务端点检查和Nacos地址

  ```
  server:
    port: 9032
  
  management:
    endpoints:
      web:
        exposure:
          include: "*"
  
  spring:
    application:
      name: sca-nacos-discovery-consumer-resttemplate
    cloud:
      nacos:
        discovery:
          server-addr: 127.0.0.1:8848
  12345678910111213141516
  ```

  #### 7、启动服务消费者

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190707205229289.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4YmluMTIz,size_16,color_FFFFFF,t_70)

  #### 8、检查nacos 控制台

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190707205317980.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4YmluMTIz,size_16,color_FFFFFF,t_70)

  #### 9、测试消费者

  ###### > 服务端点检查

  访问 http://127.0.0.1:9032/actuator/nacos-discovery
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190707205541433.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4YmluMTIz,size_16,color_FFFFFF,t_70)

  ###### > 测试服务消费API

  访问 http://localhost:9032/test

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/2019070720570810.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4YmluMTIz,size_16,color_FFFFFF,t_70)

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190707205742156.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4YmluMTIz,size_16,color_FFFFFF,t_70)