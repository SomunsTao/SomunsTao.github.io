---
title: Spring Cloud Alibaba学习笔记:3、Nacos - 服务注册与发现
date: 2020-3-13
tags: Springcloud-Alibaba
categories: Springcloud-Alibaba
keywords: Spring
description: Springcloud-Alibaba
---

- # Spring Cloud Alibaba学习笔记: 3、Nacos - 服务注册与发现

  

  ### 文章目录

  - - - [Nacos概览](https://blog.csdn.net/fxbin123/article/details/94918724#Nacos_1)

      - [什么是 Nacos？](https://blog.csdn.net/fxbin123/article/details/94918724#_Nacos_8)

      - [Nacos 概念](https://blog.csdn.net/fxbin123/article/details/94918724#Nacos__11)

      - [Nacos 架构](https://blog.csdn.net/fxbin123/article/details/94918724#Nacos__16)

      - - [基本架构及概念](https://blog.csdn.net/fxbin123/article/details/94918724#_17)

        - - [服务 (Service)](https://blog.csdn.net/fxbin123/article/details/94918724#_Service_20)
          - [服务注册中心 (Service Registry)](https://blog.csdn.net/fxbin123/article/details/94918724#_Service_Registry_23)
          - [服务元数据 (Service Metadata)](https://blog.csdn.net/fxbin123/article/details/94918724#_Service_Metadata_26)
          - [服务提供方 (Service Provider)](https://blog.csdn.net/fxbin123/article/details/94918724#_Service_Provider_29)
          - [服务消费方 (Service Consumer)](https://blog.csdn.net/fxbin123/article/details/94918724#_Service_Consumer_32)
          - [配置 (Configuration)](https://blog.csdn.net/fxbin123/article/details/94918724#_Configuration_35)
          - [配置管理 (Configuration Management)](https://blog.csdn.net/fxbin123/article/details/94918724#_Configuration_Management_38)
          - [名字服务 (Naming Service)](https://blog.csdn.net/fxbin123/article/details/94918724#_Naming_Service_41)
          - [配置服务 (Configuration Service)](https://blog.csdn.net/fxbin123/article/details/94918724#_Configuration_Service_44)

      - [安装Nacos](https://blog.csdn.net/fxbin123/article/details/94918724#Nacos_49)

      - - - [1、下载服务](https://blog.csdn.net/fxbin123/article/details/94918724#1_52)
          - [2、启动服务器](https://blog.csdn.net/fxbin123/article/details/94918724#2_54)
          - [3、访问服务](https://blog.csdn.net/fxbin123/article/details/94918724#3_81)

      - [接入 Nacos 注册中心](https://blog.csdn.net/fxbin123/article/details/94918724#_Nacos__87)

      - - [服务提供者](https://blog.csdn.net/fxbin123/article/details/94918724#_88)
        - [服务消费者](https://blog.csdn.net/fxbin123/article/details/94918724#_206)

  

  ### Nacos概览

  Nacos 官网地址： https://nacos.io

  Nacos 致力于帮助您发现、配置和管理微服务。Nacos 提供了一组简单易用的特性集，帮助您快速实现动态服务发现、服务配置、服务元数据及流量管理。

  Nacos 帮助您更敏捷和容易地构建、交付和管理微服务平台。 Nacos 是构建以“服务”为中心的现代应用架构 (例如微服务范式、云原生范式) 的服务基础设施。

  ### 什么是 Nacos？

  https://nacos.io/zh-cn/docs/what-is-nacos.html

  ### Nacos 概念

  https://nacos.io/zh-cn/docs/concepts.html

  > NOTE: Nacos 引入了一些基本的概念，系统性的了解一下这些概念可以帮助您更好的理解和正确的使用 Nacos 产品。

  ### Nacos 架构

  #### 基本架构及概念

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190707004012333.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4YmluMTIz,size_16,color_FFFFFF,t_70)

  ##### 服务 (Service)

  服务是指一个或一组软件功能（例如特定信息的检索或一组操作的执行），其目的是不同的客户端可以为不同的目的重用（例如通过跨进程的网络调用）。Nacos 支持主流的服务生态，如 Kubernetes Service、gRPC|Dubbo RPC Service 或者 Spring Cloud RESTful Service.

  ##### 服务注册中心 (Service Registry)

  服务注册中心，它是服务，其实例及元数据的数据库。服务实例在启动时注册到服务注册表，并在关闭时注销。服务和路由器的客户端查询服务注册表以查找服务的可用实例。服务注册中心可能会调用服务实例的健康检查 API 来验证它是否能够处理请求。

  ##### 服务元数据 (Service Metadata)

  服务元数据是指包括服务端点(endpoints)、服务标签、服务版本号、服务实例权重、路由规则、安全策略等描述服务的数据

  ##### 服务提供方 (Service Provider)

  是指提供可复用和可调用服务的应用方

  ##### 服务消费方 (Service Consumer)

  是指会发起对某个服务调用的应用方

  ##### 配置 (Configuration)

  在系统开发过程中通常会将一些需要变更的参数、变量等从代码中分离出来独立管理，以独立的配置文件的形式存在。目的是让静态的系统工件或者交付物（如 WAR，JAR 包等）更好地和实际的物理运行环境进行适配。配置管理一般包含在系统部署的过程中，由系统管理员或者运维人员完成这个步骤。配置变更是调整系统运行时的行为的有效手段之一。

  ##### 配置管理 (Configuration Management)

  在数据中心中，系统中所有配置的编辑、存储、分发、变更管理、历史版本管理、变更审计等所有与配置相关的活动统称为配置管理。

  ##### 名字服务 (Naming Service)

  提供分布式系统中所有对象(Object)、实体(Entity)的“名字”到关联的元数据之间的映射管理服务，例如 ServiceName -> Endpoints Info, Distributed Lock Name -> Lock Owner/Status Info, DNS Domain Name -> IP List, 服务发现和 DNS 就是名字服务的2大场景。

  ##### 配置服务 (Configuration Service)

  在服务或者应用运行过程中，提供动态配置或者元数据以及配置管理的服务提供者。

  [更多…](https://nacos.io/zh-cn/docs/architecture.html)

  ### 安装Nacos

  ```
  注： 本文采用 Nacos Docker 方式启动
  ```

  ##### 1、下载服务

  ```
   下载地址：https://github.com/alibaba/nacos/releases
  1
  ```

  ##### 2、启动服务器

  - Linux/Unix/Mac
    启动命令(standalone代表着单机模式运行，非集群模式):

  ```
     sh startup.sh -m standalone
  1
  ```

  - Windows
    启动命令：

  ```
     cmd startup.cmd
  1
  ```

  或者双击startup.cmd运行文件

  - Docker

  > 1 、Clone 项目

  ```
  	git clone https://github.com/nacos-group/nacos-docker.git
      cd nacos-docker
  12
  ```

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190707005455624.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4YmluMTIz,size_16,color_FFFFFF,t_70)

  > 2、单机模式

  ```
  	docker-compose -f example/standalone.yaml up
  1
  ```

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190707005753882.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4YmluMTIz,size_16,color_FFFFFF,t_70)

  ##### 3、访问服务

  启动完成之后，访问：http://127.0.0.1:8848/nacos/
  可以进入Nacos的服务管理页面，
  **`注`**：从 0.8.0 版本开始，需要登录才可访问，nacos 默认用户名密码 `nacos/nacos`
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190707010038347.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4YmluMTIz,size_16,color_FFFFFF,t_70)

  ### 接入 Nacos 注册中心

  #### 服务提供者

  1、 创建一个maven module 项目 `sca-nacos-discovery-provider`

  2、 pom.xml 配置如下：

  ```
  <?xml version="1.0" encoding="UTF-8"?>
  <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>
      <parent>
          <groupId>cn.fxbin.learn</groupId>
          <artifactId>sca</artifactId>
          <version>1.0</version>
      </parent>
  
      <artifactId>sca-nacos-discovery-provider</artifactId>
      <version>0.0.1-SNAPSHOT</version>
      <name>sca-nacos-discovery-provider</name>
      <description>Spring Cloud Alibaba Nacos Discovery Provider Service Project</description>
  
      <properties>
          <java.version>1.8</java.version>
      </properties>
  
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
  1234567891011121314151617181920212223242526272829303132333435363738394041
  ```

  3、创建应用主类别 `ScaNacosDiscoveryProviderApplication.java`

  ```
  package cn.fxbin.learn.sca.nacos.provider;
  
  import org.springframework.boot.SpringApplication;
  import org.springframework.boot.autoconfigure.SpringBootApplication;
  import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
  import org.springframework.web.bind.annotation.GetMapping;
  import org.springframework.web.bind.annotation.PathVariable;
  import org.springframework.web.bind.annotation.RestController;
  
  @EnableDiscoveryClient
  @SpringBootApplication
  public class ScaNacosDiscoveryProviderApplication {
  
      public static void main(String[] args) {
          SpringApplication.run(ScaNacosDiscoveryProviderApplication.class, args);
      }
  
      @RestController
      public class EchoController {
          @GetMapping(value = "/echo/{message}")
          public String echo(@PathVariable String message) {
              return "Hello Nacos Discovery Provider : " + message;
          }
      }
  }
  12345678910111213141516171819202122232425
  ```

  4、 配置服务端口、名称、服务端点检查和Nacos地址

  ```
  server:
    port: 9090
  
  management:
    endpoints:
      web:
        exposure:
          include: "*"
  
  spring:
    application:
      name: sca-nacos-discovery-provider
    cloud:
      nacos:
        discovery:
          server-addr: 127.0.0.1:8848
  12345678910111213141516
  ```

  5、启动服务提供者

  > 验证服务端点检查 ：

  spring-cloud-starter-alibaba-nacos-discovery 在实现的时候提供了一个 EndPoint, EndPoint 的访问地址为 http://ip:port/actuator/nacos-discovery。 EndPoint 的信息主要提供了两类:

  1、subscribe: 显示了当前有哪些服务订阅者
  2、NacosDiscoveryProperties: 显示了当前服务实例关于 Nacos 的基础配置

  如下图所示：
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190707013051112.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4YmluMTIz,size_16,color_FFFFFF,t_70)

  > 验证注册列表：

  启动完成之后，在 Nacos Server 控制台可以看到如下内容 ：

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190707012900808.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4YmluMTIz,size_16,color_FFFFFF,t_70)
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/2019070701291733.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4YmluMTIz,size_16,color_FFFFFF,t_70)

  #### 服务消费者

  1、 创建一个maven module 项目 `sca-nacos-discovery-consumer`

  2、 pom.xml 配置如下：

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
  
      <artifactId>sca-nacos-discovery-consumer</artifactId>
      <version>0.0.1-SNAPSHOT</version>
      <name>sca-nacos-discovery-provider</name>
      <description>Spring Cloud Alibaba Nacos Discovery Consumer Service Project</description>
  
      <properties>
          <java.version>1.8</java.version>
      </properties>
  
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
  123456789101112131415161718192021222324252627282930313233343536373839404142
  ```

  3、创建应用主类别 `ScaNacosDiscoveryConsumerApplication.java`

  ```
  package cn.fxbin.learn.sca.nacos.consumer;
  
  import lombok.extern.slf4j.Slf4j;
  import org.springframework.boot.SpringApplication;
  import org.springframework.boot.autoconfigure.SpringBootApplication;
  import org.springframework.cloud.client.ServiceInstance;
  import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
  import org.springframework.cloud.client.loadbalancer.LoadBalancerClient;
  import org.springframework.context.annotation.Bean;
  import org.springframework.web.bind.annotation.GetMapping;
  import org.springframework.web.bind.annotation.PathVariable;
  import org.springframework.web.bind.annotation.RestController;
  import org.springframework.web.client.RestTemplate;
  
  import javax.annotation.Resource;
  
  @Slf4j
  @EnableDiscoveryClient
  @SpringBootApplication
  public class ScaNacosDiscoveryConsumerApplication {
  
      public static void main(String[] args) {
          SpringApplication.run(ScaNacosDiscoveryConsumerApplication.class, args);
      }
  
      @Bean
      public RestTemplate restTemplate(){
          return new RestTemplate();
      }
  
      @RestController
      public class ConsumerController {
  
          @Resource
          LoadBalancerClient loadBalancerClient;
  
          @Resource
          private RestTemplate restTemplate;
  
  
          @GetMapping(value = "/echo/{message}")
          public String echo(@PathVariable String message) {
  
              // 通过spring cloud common中的负载均衡接口选取服务提供节点实现接口调用
              ServiceInstance serviceInstance = loadBalancerClient.choose("sca-nacos-discovery-provider");
              String url = String.format("http://%s:%s/echo/%s", serviceInstance.getHost(), serviceInstance.getPort(), " !^@^! ");
              log.info("请求路径为：{}", url);
              return restTemplate.getForObject(url, String.class);
          }
      }
  }
  123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051
  ```

  4、 配置服务端口、名称、服务端点检查和Nacos地址

  ```
  server:
    port: 9031
  
  management:
    endpoints:
      web:
        exposure:
          include: "*"
  
  spring:
    application:
      name: sca-nacos-discovery-consumer
    cloud:
      nacos:
        discovery:
          server-addr: 127.0.0.1:8848
  12345678910111213141516
  ```

  5、启动服务消费者， postman 验证接口：

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/2019070701552939.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4YmluMTIz,size_16,color_FFFFFF,t_70)

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190707015544275.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4YmluMTIz,size_16,color_FFFFFF,t_70)

  
