---
title: Springcloud-Alibaba 〖五〗ZooKeeper篇
date: 2020-04-05
tags: Springcloud-Alibaba
categories: Springcloud-Alibaba
keywords: spring
description: Springcloud-Alibaba
---



# Springcloud-Alibaba 〖五〗ZooKeeper篇 cloud-provider-payment8004模块 cloud-consumerzk-order80



### Springcloud-Alibaba 〖五〗ZooKeeper篇

- - - [PS: github仓库[仓库地址](https://github.com/ktoking/springcloud-alibaba)项目都放到里面了](https://blog.csdn.net/kingtok/article/details/105024686#PS_githubhttpsgithubcomktokingspringcloudalibaba_1)

    - [一. 安装Zookeeper](https://blog.csdn.net/kingtok/article/details/105024686#_Zookeeper_2)

    - - [1.1 压缩包](https://blog.csdn.net/kingtok/article/details/105024686#11__4)
      - [1.2 安装解压到服务器上](https://blog.csdn.net/kingtok/article/details/105024686#12__9)
      - [1.3 关闭防火墙](https://blog.csdn.net/kingtok/article/details/105024686#13__12)
      - [1.4 架构图](https://blog.csdn.net/kingtok/article/details/105024686#14__19)

    - [二. 创建新模块 cloud-provider-payment8004](https://blog.csdn.net/kingtok/article/details/105024686#__cloudproviderpayment8004_22)

    - - [2.1 修改POM文件引入依赖](https://blog.csdn.net/kingtok/article/details/105024686#21_POM_23)

      - [2.2 创建 application.yml 配置文件](https://blog.csdn.net/kingtok/article/details/105024686#22__applicationyml__75)

      - [2.3 主启动类](https://blog.csdn.net/kingtok/article/details/105024686#23__88)

      - [2.4 业务类 controller层](https://blog.csdn.net/kingtok/article/details/105024686#24__controller_105)

      - [2.5 启动会报错](https://blog.csdn.net/kingtok/article/details/105024686#25__132)

      - [2.6 测试](https://blog.csdn.net/kingtok/article/details/105024686#26__207)

      - - [2.6.1 controller层测试](https://blog.csdn.net/kingtok/article/details/105024686#261_controller_210)
        - [2.6.2 测试zk端拿到注册信息](https://blog.csdn.net/kingtok/article/details/105024686#262_zk_213)

    - [三. zk总结](https://blog.csdn.net/kingtok/article/details/105024686#_zk_218)

    - [四. 创建订单服务module cloud-consumerzk-order80](https://blog.csdn.net/kingtok/article/details/105024686#_module_cloudconsumerzkorder80_223)

    - - [4.1 创建模块](https://blog.csdn.net/kingtok/article/details/105024686#41__224)
      - [4.2 改pom](https://blog.csdn.net/kingtok/article/details/105024686#42_pom_226)
      - [4.3 建 application.yml](https://blog.csdn.net/kingtok/article/details/105024686#43__applicationyml_296)
      - [4.4 主启动](https://blog.csdn.net/kingtok/article/details/105024686#44__309)
      - [4.5 业务类](https://blog.csdn.net/kingtok/article/details/105024686#45__328)
      - [4.6 测试](https://blog.csdn.net/kingtok/article/details/105024686#46__351)

    - [Zookeeper全篇完,下一篇为 用Consul当服务注册中心~](https://blog.csdn.net/kingtok/article/details/105024686#Zookeeper_Consul_357)

  - [别白嫖啊,点赞关注呢~](https://blog.csdn.net/kingtok/article/details/105024686#_359)



### PS: github仓库[仓库地址](https://github.com/ktoking/springcloud-alibaba)项目都放到里面了

### 一. 安装Zookeeper

**zk的安装过程什么的就不废话了,这里有我之前安装的分布式的一个zk,可以参照下[zk集群安装](https://blog.csdn.net/kingtok/article/details/104311911),本篇不再强调分布式,而是使用zk作为服务中心,大家可以不用配zk集群,用单机一样的~**

#### 1.1 压缩包

[百度网盘Zookeeper](https://pan.baidu.com/s/1eUiP8LEni0TwDez1PZ1qXw)

**提取码 :ds2b**

#### 1.2 安装解压到服务器上

**这一步不做过多讲解**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200322112106289.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 1.3 关闭防火墙

- **sudo systemctl stop firewalld 临时关闭**
- **sudo systemctl disable firewalld ，然后reboot 永久关闭**
- **sudo systemctl status firewalld 查看防火墙状态。**

#### 1.4 架构图

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200322112234296.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

### 二. 创建新模块 cloud-provider-payment8004

#### 2.1 修改POM文件引入依赖

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

    <artifactId>cloud-provider-payment8004</artifactId>

    <dependencies>
        <!--springboot整合zookeeper客户端-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
        </dependency>
        <!--Springboot整合web组件-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!--引入自定义的api通用包，可以使用payment支付Entity-->
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
123456789101112131415161718192021222324252627282930313233343536373839404142434445464748
```

#### 2.2 创建 application.yml 配置文件

**IP你要填你自己服务器的IP哦**

```yml
server:
  port: 8004
spring:
  application:
    name: cloud-provider-payment
  cloud:
    zookeeper:
      connect-string: 公网IP:2181

123456789
```

#### 2.3 主启动类

```java
package com.atguigu.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

@SpringBootApplication
@EnableDiscoveryClient   //该注解用于想使用consul或者zookeeper作为注册中心时注册服务
public class PaymentMain8004 {
    public static void main(String[] args) {
        SpringApplication.run(PaymentMain8004.class,args);
    }
}
12345678910111213
```

#### 2.4 业务类 controller层

```java
package com.atguigu.springcloud.controller;

import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.UUID;

@RestController
@Slf4j
public class PaymentController {

    @Value("${server.port}")
    private String serverPort;

    @GetMapping(value = "/zk/get")
    public String paymentzk(){
        return "Spring cloud with zk port: "+serverPort+"\t"+ UUID.randomUUID().toString();
    }

}
12345678910111213141516171819202122
```

#### 2.5 启动会报错

**这里的报错是因为jar包重复了,需要我们手动的排除jar包在pom文件里,以下是我改过后经过测试的可以用的,大家可以直接复制**

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

    <artifactId>cloud-provider-payment8004</artifactId>

    <dependencies>
        <!--springboot整合zookeeper客户端-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>org.apache.zookeeper</groupId>
                    <artifactId>zookeeper</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
            <version>3.4.6</version>
            <exclusions>
                <exclusion>
                    <groupId>log4j</groupId>
                    <artifactId>log4j</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-log4j12</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <!--Springboot整合web组件-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!--引入自定义的api通用包，可以使用payment支付Entity-->
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

12345678910111213141516171819202122232425262728293031323334353637383940414243444546474849505152535455565758596061626364656667686970
```

#### 2.6 测试

**连上我们的zkclient后,查找我们注册进来的服务,可以找到就代表成功注册进来**
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020032215474120.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

##### 2.6.1 controller层测试

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200322155142693.png)

##### 2.6.2 测试zk端拿到注册信息

**输入以下命令,进入到服务里拿到一串json数据**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200322155539696.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**转一下格式,发现是我们的注册详细信息**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200322155620387.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

### 三. zk总结

**Zookeeper数据结构**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200311221410784.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxMjExNjQy,size_16,color_FFFFFF,t_70)
**zookeeper作为服务注册中心，微服务作为节点注册进来，那这个节点是临时的,只要一段时间你的服务不重新传回心跳,zk就会将你剔除,当下次你再进来继续给你注册**

### 四. 创建订单服务module cloud-consumerzk-order80

#### 4.1 创建模块

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200322171343451.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 4.2 改pom

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

    <artifactId>cloud-consumerzk-order80</artifactId>

    <dependencies>
        <!--springboot整合zookeeper客户端-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-zookeeper-discovery</artifactId>
            <!--排除自带的zookeeper-->
            <exclusions>
                <exclusion>
                    <groupId>org.apache.zookeeper</groupId>
                    <artifactId>zookeeper</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <!--添加zookeeper3.4.14版本-->
        <dependency>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
            <version>3.4.6</version>
            <exclusions>
                <exclusion>
                    <groupId>log4j</groupId>
                    <artifactId>log4j</artifactId>
                </exclusion>
                <exclusion>
                    <groupId>org.slf4j</groupId>
                    <artifactId>slf4j-log4j12</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <!--springboot整合web组件-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
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

123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051525354555657585960616263646566
```

#### 4.3 建 application.yml

**IP填你自己的服务器IP哦**

```yml
server:
  port: 80
spring:
  application:
    name: cloud-consumer-order
  cloud:
    #注册到zookeeper地址
    zookeeper:
      connect-string: 公网IP地址:2181
123456789
```

#### 4.4 主启动

```java
package com.atguigu.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

@SpringBootApplication
@EnableDiscoveryClient
public class OrderZKMain80 {
    public static void main(String[] args) {
        SpringApplication.run(OrderZKMain80.class,args);
    }
}

1234567891011121314
```

#### 4.5 业务类

**消费者需要调用 RestTemplate 来调用远程服务**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200322171635494.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

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

1234567891011121314151617
```

#### 4.6 测试

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200322171754639.png)
**在我们zk里面服务已经注册进来了**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200322171823323.png)
**调用我们的80端口服务也能访问到了~**

### Zookeeper全篇完,下一篇为 用Consul当服务注册中心~