---
title: Springcloud-Alibaba〖二〗微服务cloud-consumer-order80构建与公共类cloud-api-commons
date: 2020-04-02 08:08:08
tags: Springcloud-Alibaba
categories: Springcloud-Alibaba
keywords: spring
description: Springcloud-Alibaba
---



# Springcloud-Alibaba〖二〗微服务cloud-consumer-order80构建与公共类cloud-api-commons

### Springcloud-Alibaba 微服务cloud-consumer-order80与cloud-api-commons构建

- - - [PS: github仓库[仓库地址](https://github.com/ktoking/springcloud-alibaba)项目都放到里面了](https://blog.csdn.net/kingtok/article/details/104990775?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param#PS_githubhttpsgithubcomktokingspringcloudalibaba_1)

  - [四. 热部署(可做可不做,不做可跳过)](https://blog.csdn.net/kingtok/article/details/104990775?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param#__10)

  - - [4.1 添加依赖在8001项目中](https://blog.csdn.net/kingtok/article/details/104990775?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param#41_8001_14)
    - [4.2 配置父工程pom文件](https://blog.csdn.net/kingtok/article/details/104990775?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param#42_pom_24)
    - [4.3 开启自动编译选项](https://blog.csdn.net/kingtok/article/details/104990775?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param#43__40)
    - [4.4 配置选项](https://blog.csdn.net/kingtok/article/details/104990775?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param#44__43)

  - [五. 建立 cloud-consumer-order80微服务](https://blog.csdn.net/kingtok/article/details/104990775?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param#__cloudconsumerorder80_51)

  - - [5.1 创建模块](https://blog.csdn.net/kingtok/article/details/104990775?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param#51__52)
    - [5.2 改pom文件](https://blog.csdn.net/kingtok/article/details/104990775?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param#52_pom_54)
    - [5.3 建立yml文件](https://blog.csdn.net/kingtok/article/details/104990775?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param#53_yml_86)
    - [5.4 建立主启动](https://blog.csdn.net/kingtok/article/details/104990775?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param#54__93)
    - [5.5 引入实体类](https://blog.csdn.net/kingtok/article/details/104990775?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param#55__110)
    - [5.6 创建 RestTemplate模板加入spring的bean中](https://blog.csdn.net/kingtok/article/details/104990775?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param#56__RestTemplatespringbean_113)
    - [5.7 controller层](https://blog.csdn.net/kingtok/article/details/104990775?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param#57_controller_133)
    - [5.8 测试及坑](https://blog.csdn.net/kingtok/article/details/104990775?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param#58__172)

  - [六. 工程重构](https://blog.csdn.net/kingtok/article/details/104990775?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param#__179)

  - - [6.1 重复代码存在](https://blog.csdn.net/kingtok/article/details/104990775?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param#61__180)

    - [6.2 创建一个公共模块](https://blog.csdn.net/kingtok/article/details/104990775?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param#62__184)

    - - [6.2.1首先创建出cloud-api-commons模块](https://blog.csdn.net/kingtok/article/details/104990775?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param#621cloudapicommons_186)
      - [6.2.2 添加新模块的pom文件里的依赖](https://blog.csdn.net/kingtok/article/details/104990775?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param#622_pom_188)
      - [6.2.3 将公共实体类代码拷贝到新模块中](https://blog.csdn.net/kingtok/article/details/104990775?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param#623__226)
      - [6.2.4 Maven打包](https://blog.csdn.net/kingtok/article/details/104990775?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param#624_Maven_229)
      - [6.2.5 各自删除8001与80项目的实体包](https://blog.csdn.net/kingtok/article/details/104990775?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param#625_800180_236)
      - [6.2.6 各自导入打包完成的实体类依赖](https://blog.csdn.net/kingtok/article/details/104990775?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param#626__237)
      - [6.2.7 测试代码](https://blog.csdn.net/kingtok/article/details/104990775?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param#627__247)

  - [制作不易,转载请标注~](https://blog.csdn.net/kingtok/article/details/104990775?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel_param#_252)



### PS: github仓库[仓库地址](https://github.com/ktoking/springcloud-alibaba)项目都放到里面了

**接着上一章写**

**我摊牌了,大佬们总结的脑图给你们~**

[在线脑图点击就送~](https://www.processon.com/view/link/5e6b6342e4b0e3993b62db71#map)

## 四. 热部署(可做可不做,不做可跳过)

**每次代码写完后人工重启非常耗费时间的,所以我们选择热部署**

### 4.1 添加依赖在8001项目中

```xml
 <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-devtools</artifactId>
       <scope>runtime</scope>
       <optional>true</optional>
 </dependency>
123456
```

### 4.2 配置父工程pom文件

```xml
 <build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
        <configuration>
          <fork>true</fork>
          <addResources>true</addResources>
        </configuration>
      </plugin>
    </plugins>
  </build>
123456789101112
```

### 4.3 开启自动编译选项

**选项全勾中**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200320151651868.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

### 4.4 配置选项

**来到8001工程,打开idea,按住ctrl +shift+ alt + /**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200320151919492.png)
**点开,然后选择选项打勾**
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020032015230186.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

**热部署部署完成后,你每次修改一次代码,都会重启我们整个项目,所以开发可以用,但是一旦上生产环境,千万要关闭**

## 五. 建立 cloud-consumer-order80微服务

### 5.1 创建模块

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200320153656486.png)

### 5.2 改pom文件

```xml
 <dependencies>
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
12345678910111213141516171819202122232425262728
```

### 5.3 建立yml文件

```yml
server:
  port: 80
12
```

### 5.4 建立主启动

```java
package com.aiguigu.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class OrderMain80 {
    public static void main(String[] args) {
        SpringApplication.run(OrderMain80.class,args);
    }
}

123456789101112
```

### 5.5 引入实体类

还是之前8001项目的两个实体类
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200320162858561.png)

### 5.6 创建 RestTemplate模板加入spring的bean中

```java
package com.aiguigu.springcloud.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.client.RestTemplate;

@Configuration
public class ApplicationContextConfig {

    @Bean
    public RestTemplate getRestTemplate(){
        return new RestTemplate();
    }
}

123456789101112131415
```

### 5.7 controller层

```java
package com.aiguigu.springcloud.controller;

import com.aiguigu.springcloud.entities.CommonResult;
import com.aiguigu.springcloud.entities.Payment;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

import javax.annotation.Resource;

@RestController
@Slf4j
public class OrderController {

    public static final String PAYMENT_URL= "http://localhost:8001";

    @Resource
    private RestTemplate restTemplate;

    @GetMapping("/consumer/payment/create")
    public CommonResult<Payment> create(Payment payment){
        log.info("*******消费者启动创建订单*******");
        return restTemplate.postForObject(PAYMENT_URL+"/payment/create",payment,CommonResult.class);
    }

    @GetMapping("/consumer/payment/get/{id}")
    public CommonResult<Payment> getPayment(@PathVariable("id") Long id){
        return restTemplate.getForObject(PAYMENT_URL+"/payment/get/"+id,CommonResult.class);
    }

}

12345678910111213141516171819202122232425262728293031323334
```

### 5.8 测试及坑

**测试查找成功**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200320164624867.png)

**测试一下插入发现成功了,但是数据库没值,只有自增主键,这里插入没成功的原因是,8001的创建参数没加@RequestBody,大家记得加上才能映射成功**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200320165356780.png)

## 六. 工程重构

### 6.1 重复代码存在

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200320170148920.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

### 6.2 创建一个公共模块

#### 6.2.1首先创建出cloud-api-commons模块

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200320174808869.png)

#### 6.2.2 添加新模块的pom文件里的依赖

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

    <artifactId>cloud-api-commons</artifactId>

    <dependencies>
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
            <groupId>cn.hutool</groupId>
            <artifactId>hutool-all</artifactId>
            <version>5.1.0</version>
        </dependency>
    </dependencies>
</project>

123456789101112131415161718192021222324252627282930313233
```

#### 6.2.3 将公共实体类代码拷贝到新模块中

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200320174922189.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 6.2.4 Maven打包

**先clean,记得提前点闪电跳过测试**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200320175300471.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**再点install**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200320175407468.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**成功**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200320175517660.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 6.2.5 各自删除8001与80项目的实体包

#### 6.2.6 各自导入打包完成的实体类依赖

**引入如下依赖在8001项目与80项目中**

```xml
<dependency>
	<groupId>com.atguigu.springcloud</groupId>
    <artifactId>cloud-api-commons</artifactId>
    <version>${project.version}</version>
</dependency>
12345
```

#### 6.2.7 测试代码

**测试成功**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200320182640207.png)