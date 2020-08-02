---
title: Springcloud-Alibaba 〖十一〗SpringConfig 主配置中心与从配置中心单价,动态刷新实现
date: 2020-04-11
tags: Springcloud-Alibaba 
categories: Springcloud-Alibaba 
keywords: spring
description: Springcloud-Alibaba 
---



# Springcloud-Alibaba 〖十一〗SpringConfig 主配置中心与从配置中心单价,动态刷新实现

### Springcloud-Alibaba 〖十一〗Config

- - - [PS: github仓库[仓库地址](https://github.com/ktoking/springcloud-alibaba)项目都放到里面了](https://blog.csdn.net/kingtok/article/details/105178367#PS_githubhttpsgithubcomktokingspringcloudalibaba_1)

    - [一. 分布式架构面临的问题](https://blog.csdn.net/kingtok/article/details/105178367#__2)

    - [二. Config能干吗?](https://blog.csdn.net/kingtok/article/details/105178367#_Config_10)

    - [三. 新建module cloud-config-center-3344，是cloud的配置中心模块](https://blog.csdn.net/kingtok/article/details/105178367#__module_cloudconfigcenter3344cloud_17)

    - - [3.1 项目目录](https://blog.csdn.net/kingtok/article/details/105178367#31__19)
      - [3.2 pom](https://blog.csdn.net/kingtok/article/details/105178367#32_pom_21)
      - [3.3 application.yml](https://blog.csdn.net/kingtok/article/details/105178367#33_applicationyml_74)
      - [3.4 主启动类](https://blog.csdn.net/kingtok/article/details/105178367#34__100)
      - [3.5 测试](https://blog.csdn.net/kingtok/article/details/105178367#35__118)
      - [3.6 总结](https://blog.csdn.net/kingtok/article/details/105178367#36__129)

    - [四. 新建客户端module cloud-config-client-3355](https://blog.csdn.net/kingtok/article/details/105178367#__module_cloudconfigclient3355_134)

    - - [4.1 项目目录](https://blog.csdn.net/kingtok/article/details/105178367#41__143)
      - [4.2 pom](https://blog.csdn.net/kingtok/article/details/105178367#42_pom_146)
      - [4.3 bootstrap.yml](https://blog.csdn.net/kingtok/article/details/105178367#43_bootstrapyml_202)
      - [4.4 主启动](https://blog.csdn.net/kingtok/article/details/105178367#44__229)
      - [4.5 业务类](https://blog.csdn.net/kingtok/article/details/105178367#45__247)
      - [4.6 测试](https://blog.csdn.net/kingtok/article/details/105178367#46__275)

    - [五. 分布式配置动态刷新](https://blog.csdn.net/kingtok/article/details/105178367#__279)

    - - [5.1 测试](https://blog.csdn.net/kingtok/article/details/105178367#51__294)
      - [5.2 但是还是存在问题~](https://blog.csdn.net/kingtok/article/details/105178367#52__310)

    - [哈哈,下一篇消息总线见~本篇SpringConfig完](https://blog.csdn.net/kingtok/article/details/105178367#SpringConfig_316)

    - [关注+点赞不迷路,转载请标注!](https://blog.csdn.net/kingtok/article/details/105178367#_317)



### PS: github仓库[仓库地址](https://github.com/ktoking/springcloud-alibaba)项目都放到里面了

### 一. 分布式架构面临的问题

微服务意味着要将单体应用中的业务拆分成一个个子服务, 每个服务的粒度相对较小，因此系统中会出现大量的服务。于每个服务都需要必要的配置信息才能运行，**所以一套集中式的、动态的配置管理设施是必不可少的**。

**配置中心架构图**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329202944157.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

**SpringCloud Config为微服务架构中的微服务提供集中化的外部配置支持,配置服务器为各个不同微服务应用的所有环境提供了一个中心化的外部配置。**

### 二. Config能干吗?

- **集中管理配置文件**
- **不同环境不同配置，动态化的配置更新，分环境部署比如dev/test/prod/beta/release**
- **运行期间动态调整配置，不再需要在每个服务部署的机器上编写配置文件,服务会向配置中心统一一拉取配置自己的信息**
- **当配置发生变动时，服务不需要重启即可感知到配置的变化并应用新的配置**
- **将配置信息以REST接口的形式暴露**

### 三. 新建module cloud-config-center-3344，是cloud的配置中心模块

#### 3.1 项目目录

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329181241974.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

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

    <artifactId>cloud-config-center-3344</artifactId>

    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-config-server</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
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
      
12345678910111213141516171819202122232425262728293031323334353637383940414243444546474849
```

#### 3.3 application.yml

```yml
server:
  port: 3344

spring:
  application:
    name: cloud-config-center
  cloud:
    config:
      server:
        git:
          uri: https://github.com/你自己的仓库名/springcloud-config.git #github仓库上面的git仓库名字
          ##搜索目录
          search-paths:
            - springcloud-config
      #读取分支
      label: master

eureka:
  client:
    service-url:
      defaultZone: http://localhost:7001/eureka #注册进eureka

12345678910111213141516171819202122
```

#### 3.4 主启动类

```java
package com.atguigu.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.config.server.EnableConfigServer;

@EnableConfigServer
@SpringBootApplication
public class ConfigCenterMain3344 {
    public static void main(String[] args) {
        SpringApplication.run(ConfigCenterMain3344.class,args);
    }
}

1234567891011121314
```

#### 3.5 测试

**这里首先要去github新建一个仓库**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329183638541.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**启动7001与3344项目**

**访问http://localhost:3344/master/config-dev.yml可以看见到我们已经可以访问到github上的文件了**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329181538183.png)
**若是找不到配置,就会返回一个空的{}**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329182723377.png)

#### 3.6 总结

**之前在yml配置文件里面是这样配置的**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329182258589.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**当启动我们的3344项目时,可以通过三种方法找到配置文件并读取**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329182031482.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

### 四. 新建客户端module cloud-config-client-3355

- **applicaiton. yml是用户级的资源配置项**
- **bootstrap. yml是系统级的，优先级更加高**

Spring Clould会创建一个"Bootstrap Context" , 作为Spring应用的Application Context的父上下文。初始化的时候，BootstrapContext’负责从外部源加载配置属性并解析配置。这两个上下文共享一个从外部获取的Environment。
**Bootstrap’属性有高优先级，默认情况下，它们不会被本地配置覆盖。** Bootstrap context’和Application Context有着不同的约定，所以新增了一个bootstrap.ymI文件, 保证Bootstrap Context’和Application Context配置的分离。
要将Client模块下的application.yml文件改为bootstrap.yml,这是很关键的,
因为**bootstrap.ym是比application.yml先加载的**。bootstrap.yml优先级高于application.yml

#### 4.1 项目目录

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329195610195.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 4.2 pom

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

    <artifactId>cloud-config-client-3355</artifactId>


    <dependencies>
        <!--不带server了，说明是客户端-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-config</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
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

123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051
```

#### 4.3 bootstrap.yml

```yml
server:
  port: 3355

spring:
  application:
    name: config-client
  cloud:
    #Config客户端配置
    config:
      label: master #分支名称
      name: config #配置文件名称
      profile: dev #读取后缀名称 上述3个综合：master分支上config-dev.yml的配置文件被读取 http://config-3344.com:3344/master/config-dev.yml
      uri: http://localhost:3344 #配置中心地址 表示通过这个服务端访问


#服务注册到eureka地址
eureka:
  client:
    register-with-eureka: true
    service-url:
      defaultZone: http://localhost:7001.com/eureka

12345678910111213141516171819202122
```

#### 4.4 主启动

```java
package com.atguigu.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@EnableEurekaClient
@SpringBootApplication
public class ConfigCenterMain3355 {
    public static void main(String[] args) {
        SpringApplication.run(ConfigCenterMain3355.class,args);
    }
}

1234567891011121314
```

#### 4.5 业务类

**controller层**

```java
package com.atguigu.springcloud.controller;

import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.cloud.context.config.annotation.RefreshScope;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class ConfigClientController {

    @Value("${config.info}")
    private String configInfo;  //要访问的3344上的信息

    @GetMapping("/configInfo")	//请求地址
    public String getConfigInfo(){
        return configInfo;
    }

}

123456789101112131415161718192021
```

**这里我们返回从3344主配置中心获取的config.info配置值**

#### 4.6 测试

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329195939707.png)
**成功实现了客户端3355访问SpringCloud Config3344通过GitHub获取配置信息**

### 五. 分布式配置动态刷新

**yml配置文件新增**

```yml
#暴露监控端点
management:
  endpoints:
    web:
      exposure:
        include: "*"
123456
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329201856130.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**controller层新增一个注解@RefreshScope**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329201944115.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 5.1 测试

**这里我们修改以下github上配置的版本信息**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329202149800.png)
**再来访问一下3344项目的总配置,发现改变了**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329202325832.png)
**当我们访问3355项目的得到配置呢?**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329202438105.png)
**发现还是1,这里就需要给3355发一条post请求来激活动态配置**

这里往命令窗口扔一条命令即可

```shell
curl -X POST "http://localhost:3355/actuator/refresh"
1
```

**成功刷新配置信息**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329202733574.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200329202752392.png)

#### 5.2 但是还是存在问题~

- **假如有多个微服务客户端3355/3366/3377。。。。。。+**
- **每个微服务都要执行一次post请求 ，手动刷新?**
- **可否广播，一次通知，处处生效?**
- **我们想大范围刷新呢?**

### 哈哈,下一篇消息总线见~本篇SpringConfig完

### 关注+点赞不迷路,转载请标注!