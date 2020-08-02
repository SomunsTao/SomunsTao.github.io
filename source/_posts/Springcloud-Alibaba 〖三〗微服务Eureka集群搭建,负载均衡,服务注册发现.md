---
title: Springcloud-Alibaba 〖三〗微服务Eureka集群搭建,负载均衡,服务注册发现
date: 2020-04-03 08:08:08
tags: Springcloud-Alibaba
categories: Springcloud-Alibaba
keywords: spring
description: Springcloud-Alibaba
---



# Springcloud-Alibaba 〖三〗微服务Eureka集群搭建,负载均衡,服务注册发现

### Springcloud-Alibaba 〖三〗微服务Eureka集群搭建,负载均衡,服务注册发现

- - - [PS: github仓库[仓库地址](https://github.com/ktoking/springcloud-alibaba)项目都放到里面了](https://blog.csdn.net/kingtok/article/details/105005139#PS_githubhttpsgithubcomktokingspringcloudalibaba_1)

  - [六. cloud-eureka-server7001模块构建](https://blog.csdn.net/kingtok/article/details/105005139#_cloudeurekaserver7001_4)

  - - [6.1 Eureka系统架构](https://blog.csdn.net/kingtok/article/details/105005139#61_Eureka_5)
    - [6.2 创建Eureka模块](https://blog.csdn.net/kingtok/article/details/105005139#62_Eureka_7)
    - [6.3 pom文件里添加依赖](https://blog.csdn.net/kingtok/article/details/105005139#63_pom_9)
    - [6.4 application.yml](https://blog.csdn.net/kingtok/article/details/105005139#64_applicationyml_69)
    - [6.5 主启动类](https://blog.csdn.net/kingtok/article/details/105005139#65__90)
    - [6.6 打开 http://localhost:7001/ 进入UI界面](https://blog.csdn.net/kingtok/article/details/105005139#66__httplocalhost7001_UI_110)

  - [七. 服务注册](https://blog.csdn.net/kingtok/article/details/105005139#__114)

  - - [7.1 支付微服务8001入驻进eurekaServer](https://blog.csdn.net/kingtok/article/details/105005139#71_8001eurekaServer_115)

    - - [7.1.1 增加新依赖](https://blog.csdn.net/kingtok/article/details/105005139#711__116)
      - [7.1.2 application.yml 里面添加Eureka配置](https://blog.csdn.net/kingtok/article/details/105005139#712_applicationyml_Eureka_125)
      - [7.1.3 主启动添加 @EnableEurekaClient](https://blog.csdn.net/kingtok/article/details/105005139#713__EnableEurekaClient_138)
      - [7.1.4 启动测试](https://blog.csdn.net/kingtok/article/details/105005139#714__140)

    - [7.2 订单微服务80入驻进eurekaServer](https://blog.csdn.net/kingtok/article/details/105005139#72_80eurekaServer_144)

    - - [7.2.1 增加新依赖](https://blog.csdn.net/kingtok/article/details/105005139#721__145)
      - [7.2.2 application.yml 里面添加Eureka配置](https://blog.csdn.net/kingtok/article/details/105005139#722_applicationyml_Eureka_154)
      - [7.2.3 主启动添加 @EnableEurekaClient](https://blog.csdn.net/kingtok/article/details/105005139#723___EnableEurekaClient_169)
      - [7.2.4 启动测试](https://blog.csdn.net/kingtok/article/details/105005139#724__171)

  - [八. Eureka集群搭建](https://blog.csdn.net/kingtok/article/details/105005139#_Eureka_175)

  - - [8.1 cloud-eureka-server7002模块构建](https://blog.csdn.net/kingtok/article/details/105005139#81_cloudeurekaserver7002_176)

    - - [8.1.1 创建module cloud-eureka-server7002](https://blog.csdn.net/kingtok/article/details/105005139#811_module_cloudeurekaserver7002_177)
      - [8.1.2 pom文件添加依赖](https://blog.csdn.net/kingtok/article/details/105005139#812_pom_179)
      - [8.1.3 添加主启动类](https://blog.csdn.net/kingtok/article/details/105005139#813__241)
      - [8.1.4 添加 application.yml 时先修改本机端口映射](https://blog.csdn.net/kingtok/article/details/105005139#814__applicationyml__243)

    - [8.2 修改7001和7002的application.yml](https://blog.csdn.net/kingtok/article/details/105005139#82_70017002applicationyml_256)

    - - [8.2.1 修改7001 application.yml,直接粘贴](https://blog.csdn.net/kingtok/article/details/105005139#821_7001_applicationyml_257)
      - [8.2.2 修改7002 application.yml,直接粘贴](https://blog.csdn.net/kingtok/article/details/105005139#822_7002_applicationyml_275)

    - [8.3 测试集群](https://blog.csdn.net/kingtok/article/details/105005139#83__292)

  - [九. 将两个微服务发布到Eureka集群配置中](https://blog.csdn.net/kingtok/article/details/105005139#_Eureka_298)

  - - [9.1 8001项目 application.yml](https://blog.csdn.net/kingtok/article/details/105005139#91_8001_applicationyml_299)
    - [9.2 80项目 application.yml](https://blog.csdn.net/kingtok/article/details/105005139#92_80_applicationyml_305)
    - [9.3 测试](https://blog.csdn.net/kingtok/article/details/105005139#93__310)

  - [十. 创建module cloud-provider-payment8002](https://blog.csdn.net/kingtok/article/details/105005139#__module_cloudproviderpayment8002_319)

  - - [10.1 改POM](https://blog.csdn.net/kingtok/article/details/105005139#101_POM_322)

    - [10.2 建 application.yml 文件](https://blog.csdn.net/kingtok/article/details/105005139#102__applicationyml__323)

    - [10.3 主启动](https://blog.csdn.net/kingtok/article/details/105005139#103__324)

    - [10.4 业务类](https://blog.csdn.net/kingtok/article/details/105005139#104__325)

    - [10.5 修改 Controller](https://blog.csdn.net/kingtok/article/details/105005139#105__Controller_327)

    - [10.6 开启负载均衡](https://blog.csdn.net/kingtok/article/details/105005139#106__332)

    - - [10.6.1 首先到80服务下去修改写死的对外暴露的服务url为Eureka注册中心的名字](https://blog.csdn.net/kingtok/article/details/105005139#1061_80urlEureka_333)
      - [10.6.2 在我们80项目的restTemplate加入spring容器的bean中增加一个负载均衡的注解 @LoadBalanced](https://blog.csdn.net/kingtok/article/details/105005139#1062_80restTemplatespringbean_LoadBalanced_336)
      - [10.6.3 测试](https://blog.csdn.net/kingtok/article/details/105005139#1063__339)

  - [制作不易,转载请标注,别白嫖啊~~](https://blog.csdn.net/kingtok/article/details/105005139#_346)



### PS: github仓库[仓库地址](https://github.com/ktoking/springcloud-alibaba)项目都放到里面了

**本篇章的第三章**

## 六. cloud-eureka-server7001模块构建

### 6.1 Eureka系统架构

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321103639323.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

### 6.2 创建Eureka模块

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321105339340.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

### 6.3 pom文件里添加依赖

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

    <artifactId>cloud-eureka-server7001</artifactId>
    <dependencies>
        <!--eureka server-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
        <!--引入自己定义的api通用包，可以使用payment支付Entity-->
        <dependency>
            <groupId>com.atguigu.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>${project.version}</version>
        </dependency>
        <!--boot web actuator-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--一般通用配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
        </dependency>
    </dependencies>
</project>

1234567891011121314151617181920212223242526272829303132333435363738394041424344454647484950515253545556
```

### 6.4 application.yml

```yml
server:
  port: 7001

eureka:
  instance:
    hostname: localhost #eureka服务端的实例名称
  client:
    #false表示不向注册中心注册自己
    register-with-eureka: false
    #false表示自己端就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
    fetch-registry: false
    service-url:
      #设置与Eureka Server交互的地址查询服务和注册服务都需要依赖这个地址
      defaultZ4 one: http://${eureka.instance.hostname}:${server.port}/eureka/


12345678910111213141516
```

### 6.5 主启动类

```java
package com.atguigu.springcloud;


import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;


@SpringBootApplication
@EnableEurekaServer
public class EurekaMain7001 {
    public static void main(String[] args) {
        SpringApplication.run(EurekaMain7001.class,args);
    }
}

12345678910111213141516
```

### 6.6 打开 http://localhost:7001/ 进入UI界面

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321110257757.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**此时还没有服务注册进来**

## 七. 服务注册

### 7.1 支付微服务8001入驻进eurekaServer

#### 7.1.1 增加新依赖

```xml
<!--eureka-client-->
<dependency>
	<groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>

123456
```

#### 7.1.2 application.yml 里面添加Eureka配置

```yml
eureka:
  client:
    #表示是否将自己注册进EurekaServer默认为true
    register-with-eureka: true
    #是否从EurekaServer抓取已有的注册信息，默认为true。单节点无所谓，集群必须设置为true才能配合ribbon使用负载均衡
    fetch-registry: true
    service-url:
      defaultZone: http://localhost:7001/eureka

123456789
```

#### 7.1.3 主启动添加 @EnableEurekaClient

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321111324134.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 7.1.4 启动测试

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321111618618.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**可以看到8001服务注册进来了**

### 7.2 订单微服务80入驻进eurekaServer

#### 7.2.1 增加新依赖

```xml
<!--eureka-client-->
<dependency>
	<groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>

123456
```

#### 7.2.2 application.yml 里面添加Eureka配置

```yml
spring:
  application:
    name: cloud-order-server

eureka:
  client:
    #表示是否将自己注册进EurekaServer默认为true
    register-with-eureka: true
    fetch-registry: true
    service-url:
      defaultZone: http://localhost:7001/eureka
1234567891011
```

#### 7.2.3 主启动添加 @EnableEurekaClient

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321112519936.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 7.2.4 启动测试

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020032111390769.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**服务已经注册进来**

## 八. Eureka集群搭建

### 8.1 cloud-eureka-server7002模块构建

#### 8.1.1 创建module cloud-eureka-server7002

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321155531138.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 8.1.2 pom文件添加依赖

**与7001项目依赖一样**

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

    <artifactId>cloud-eureka-server7002</artifactId>

    <dependencies>
        <!--eureka server-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
        <!--引入自己定义的api通用包，可以使用payment支付Entity-->
        <dependency>
            <groupId>com.atguigu.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>${project.version}</version>
        </dependency>
        <!--boot web actuator-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--一般通用配置-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
        </dependency>
    </dependencies>
</project>

123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051525354555657
```

#### 8.1.3 添加主启动类

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321155703508.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 8.1.4 添加 application.yml 时先修改本机端口映射

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321161045380.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**在HOSTS文件底添加端口映射**

```shell
127.0.0.1   eureka7001.com
127.0.0.1   eureka7002.com
12
```

**修改不了文件没权限的可以参照以下链接去修改**

[解决问题](https://jingyan.baidu.com/article/425e69e6e479a2be15fc16e1.html)

### 8.2 修改7001和7002的application.yml

#### 8.2.1 修改7001 application.yml,直接粘贴

```yml
server:
  port: 7001

eureka:
  instance:
    hostname: eureka7001.com  #eureka服务端的实例名称
  client:
    #false表示不向注册中心注册自己
    register-with-eureka: false
    #false表示自己端就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
    fetch-registry: false
    service-url:
      #设置与Eureka Server交互的地址查询服务和注册服务都需要依赖这个地址
      defaultZone: http://eureka7002.com:7002/eureka/
1234567891011121314
```

#### 8.2.2 修改7002 application.yml,直接粘贴

```yml
server:
  port: 7002

eureka:
  instance:
    hostname: eureka7002.com #eureka服务端的实例名称
  client:
    #false表示不向注册中心注册自己
    register-with-eureka: false
    #false表示自己端就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
    fetch-registry: false
    service-url:
      #设置与Eureka Server交互的地址查询服务和注册服务都需要依赖这个地址
      defaultZone: http://eureka7001.com:7001/eureka/
1234567891011121314
```

### 8.3 测试集群

**在7002里能看见7001既守望7001端口,就算配置成功**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321163551515.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

## 九. 将两个微服务发布到Eureka集群配置中

### 9.1 8001项目 application.yml

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321164121417.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**添加如下**

```yml
 defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/
1
```

### 9.2 80项目 application.yml

**不同的地点,添加相同的配置**

```yml
 defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/
1
```

### 9.3 测试

**服务都已注册进来**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321164444771.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

------

![加粗样式](https://img-blog.csdnimg.cn/20200321164510998.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**测试单个服务也成功**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321164732150.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

## 十. 创建module cloud-provider-payment8002

**这个模块跟8001模块一样的只不过端口不一样,所以前期创建模块我就省略步骤了,大家copy即可,我把步骤贴出来**

### 10.1 改POM

### 10.2 建 application.yml 文件

### 10.3 主启动

### 10.4 业务类

### 10.5 修改 Controller

**8001 Controller**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321181923251.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**8002 Controller**.
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321181945443.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

### 10.6 开启负载均衡

#### 10.6.1 首先到80服务下去修改写死的对外暴露的服务url为Eureka注册中心的名字

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321201659964.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321201139749.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 10.6.2 在我们80项目的restTemplate加入spring容器的bean中增加一个负载均衡的注解 @LoadBalanced

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321201249540.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 10.6.3 测试

**负载均衡测试成功**

- **8002端口**
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321201809465.png)
- **8001端口**
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321201847164.png)