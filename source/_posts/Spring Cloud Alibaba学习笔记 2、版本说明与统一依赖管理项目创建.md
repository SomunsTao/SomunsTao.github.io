---
title: Spring Cloud Alibaba学习笔记:2、版本说明与统一依赖管理项目创建
date: 2020-3-12
tags: Springcloud-Alibaba
categories: Springcloud-Alibaba
keywords: Spring 
description: Springcloud-Alibaba
---

- # Spring Cloud Alibaba学习笔记: 2、版本说明与统一依赖管理项目创建

  

  ### 文章目录

  - - [版本使用列表](https://blog.csdn.net/fxbin123/article/details/94918049#_3)
    - [Spring Boot、Spring Cloud、Spring Cloud Alibaba三者间关系](https://blog.csdn.net/fxbin123/article/details/94918049#Spring_BootSpring_CloudSpring_Cloud_Alibaba_15)
    - [项目结构图](https://blog.csdn.net/fxbin123/article/details/94918049#_25)
    - [创建依赖管理项目](https://blog.csdn.net/fxbin123/article/details/94918049#_28)

  

  ## 版本使用列表

  Spring Cloud Alibaba 项目都是基于 Spring Cloud，而 Spring Cloud 项目又是基于 Spring Boot 进行开发，并且都是使用 Maven 做项目管理工具，以下为我们使用的版本列表：

  | 名称                 | 版本          |
  | -------------------- | ------------- |
  | JAVA JDK             | 1.8.0_211     |
  | Spring Boot          | 2.1.6.RELEASE |
  | Spring Boot Admin    | 2.1.5         |
  | Spring Cloud         | Greenwich.SR2 |
  | Spring Cloud Alibaba | 0.9.0.RELEASE |

  ## Spring Boot、Spring Cloud、Spring Cloud Alibaba三者间关系

  | Spring Boot | Spring Cloud | Spring Cloud Alibaba |
  | ----------- | ------------ | -------------------- |
  | 2.1.x       | Greenwich    | 0.9.x                |
  | 2.0.x       | Finchley     | 0.2.x                |
  | 1.5.x       | Edgware      | 0.1.x                |
  | 1.5.x       | Dalston      | 0.1.x                |

  参考：

  ## 项目结构图

  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190707000245484.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Z4YmluMTIz,size_16,color_FFFFFF,t_70)

  ## 创建依赖管理项目

  创建一个 `sca` （Spring Cloud Alibaba 首字母缩写） 的项目， 以下为 `pom.xml` 依赖文件内容：

  ```
  <?xml version="1.0" encoding="UTF-8"?>
  <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>
      <parent>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-parent</artifactId>
          <version>2.1.6.RELEASE</version>
          <relativePath/> <!-- lookup parent from repository -->
      </parent>
  
      <groupId>cn.fxbin.learn</groupId>
      <artifactId>sca</artifactId>
      <version>1.0</version>
      <packaging>pom</packaging>
  
      <name>sca-build</name>
      <description>Spring Cloud Alibaba Learn Build Project</description>
  
  
      <properties>
          <!-- Environment Settings -->
          <java.version>1.8</java.version>
          <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
          <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
          
          <!-- Spring Settings -->
          <spring-boot-admin.version>2.1.5</spring-boot-admin.version>
          <spring-cloud.version>Greenwich.SR2</spring-cloud.version>
          <spring-cloud-alibaba.version>0.9.0.RELEASE</spring-cloud-alibaba.version>
      </properties>
  
  
      <dependencies>
  
          <!-- actuator -->
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-actuator</artifactId>
          </dependency>
  
          <!-- lombok -->
          <dependency>
              <groupId>org.projectlombok</groupId>
              <artifactId>lombok</artifactId>
              <optional>true</optional>
          </dependency>
  
          <!-- test -->
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-test</artifactId>
              <scope>test</scope>
          </dependency>
  
      </dependencies>
  
      <dependencyManagement>
          <dependencies>
              <!-- spring-boot-admin -->
              <dependency>
                  <groupId>de.codecentric</groupId>
                  <artifactId>spring-boot-admin-dependencies</artifactId>
                  <version>${spring-boot-admin.version}</version>
                  <type>pom</type>
                  <scope>import</scope>
              </dependency>
  
              <!-- spring cloud -->
              <dependency>
                  <groupId>org.springframework.cloud</groupId>
                  <artifactId>spring-cloud-dependencies</artifactId>
                  <version>${spring-cloud.version}</version>
                  <type>pom</type>
                  <scope>import</scope>
              </dependency>
  
              <!-- spring cloud alibaba -->
              <dependency>
                  <groupId>org.springframework.cloud</groupId>
                  <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                  <version>${spring-cloud-alibaba.version}</version>
                  <type>pom</type>
                  <scope>import</scope>
              </dependency>
          </dependencies>
      </dependencyManagement>
  
      <build>
          <plugins>
              <plugin>
                  <groupId>org.springframework.boot</groupId>
                  <artifactId>spring-boot-maven-plugin</artifactId>
              </plugin>
          </plugins>
  
      </build>
  
  
  </project>
  ```

