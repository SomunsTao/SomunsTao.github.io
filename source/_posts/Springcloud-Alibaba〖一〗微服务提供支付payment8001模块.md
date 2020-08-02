---
title: Springcloud-Alibaba〖一〗微服务提供支付payment8001模块
date: 2020-04-01 08:08:08
tags: Springcloud-Alibaba
categories: Springcloud-Alibaba
keywords: spring
description: Springcloud-Alibaba
---



# Springcloud-Alibaba〖一〗微服务提供支付payment8001模块



### Springcloud-Alibaba 微服务提供支付payment8001模块

- - - [PS: github仓库[仓库地址](https://github.com/ktoking/springcloud-alibaba)项目都放到里面了](https://blog.csdn.net/kingtok/article/details/104963122#PS_githubhttpsgithubcomktokingspringcloudalibaba_4)

  - [一. 技术选型.](https://blog.csdn.net/kingtok/article/details/104963122#__5)

  - [二. 父工程POM聚合](https://blog.csdn.net/kingtok/article/details/104963122#_POM_15)

  - [三. 建立支付module: cloud-provider-payment8001](https://blog.csdn.net/kingtok/article/details/104963122#_module_cloudproviderpayment8001_132)

  - - [3.1 项目结构](https://blog.csdn.net/kingtok/article/details/104963122#31__133)

    - [3.2 建立子module的pom文件](https://blog.csdn.net/kingtok/article/details/104963122#32_modulepom_135)

    - [3.3 子模块的yml配置文件](https://blog.csdn.net/kingtok/article/details/104963122#33_yml_198)

    - [3.4 建库](https://blog.csdn.net/kingtok/article/details/104963122#34__219)

    - [3.5 业务类](https://blog.csdn.net/kingtok/article/details/104963122#35__230)

    - - [3.5.1 目录结构](https://blog.csdn.net/kingtok/article/details/104963122#351__231)
      - [3.5.2 实体类](https://blog.csdn.net/kingtok/article/details/104963122#352__233)
      - [3.5.3 service及实现类](https://blog.csdn.net/kingtok/article/details/104963122#353_service_264)
      - [3.5.4 Controller实现](https://blog.csdn.net/kingtok/article/details/104963122#354_Controller_312)
      - [3.5.5 测试启动](https://blog.csdn.net/kingtok/article/details/104963122#355__362)

    - [3.6 建一个模块的总结](https://blog.csdn.net/kingtok/article/details/104963122#36__375)

    - [制作不易,转载请标注~~](https://blog.csdn.net/kingtok/article/details/104963122#_384)


**废话不多说,搞一点阳间的东西玩玩,大伙们懂得都懂哦~**
**此章节及之后Springcloud-Alibaba博客参照尚硅谷周阳老师视频[视频链接](https://www.bilibili.com/video/av93813318?p=11),加上自己理解整理所成,点赞加关注给你们脑图,下一篇博客就更脑图!**



### PS: github仓库[仓库地址](https://github.com/ktoking/springcloud-alibaba)项目都放到里面了

## 一. 技术选型.

| 技术          | 版本          |
| ------------- | ------------- |
| cloud         | Hoxton        |
| boot          | 2.2.2.RELEASE |
| cloud alibaba | 2.1.0.RELEASE |
| java          | 1.8           |
| Maven         | 3.5.2         |
| Mysql         | 5.7及以上     |

## 二. 父工程POM聚合

maven架构选择org.apache.maven.archetypes:maven-archetype-site

- 删掉其中的src目录,因为我们这个项目是父工程,只提供版本控制
- **< dependencyManagement >** 标签 子模块继承之后，提供作用：锁定版本+子模块不用groupId和version,Maven工程会沿着父子层向上走,直到找到一个**dependencyManagement**元素的项目,然后他就会使用这个元素指定的版本号
- **< packaging>pom< /packaging >** POM是最简单的打包类型。不像一个JAR，SAR，或者EAR，它生成的构件只是它本身。没有代码需要测试或者编译，也没有资源需要处理。

**pom文件如下,注释基本都有**

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.aiguigu.springcloud</groupId>
  <artifactId>cloud2020</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>pom</packaging> 

  <name>Maven</name>
  <!-- FIXME change it to the project's website -->
  <url>http://maven.apache.org/</url>
  <inceptionYear>2001</inceptionYear>

  <distributionManagement>
    <site>
      <id>website</id>
      <url>scp://webhost.company.com/www/website</url>
    </site>
  </distributionManagement>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <junit.version>4.12</junit.version>
    <log4j.version>1.2.17</log4j.version>
    <lombok.version>1.16.18</lombok.version>
    <mysql.version>5.1.47</mysql.version>
    <druid.version>1.1.16</druid.version>
    <mybatis.spring.boot.version>1.3.0</mybatis.spring.boot.version>
  </properties>

  <!--子模块继承之后，提供作用：锁定版本+子module不用groupId和version-->
  <dependencyManagement>
    <dependencies>
      <!--spring boot 2.2.2-->
      <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-dependencies</artifactId>
        <version>2.2.2.RELEASE</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
      <!--spring cloud Hoxton.SR1-->
      <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-dependencies</artifactId>
        <version>Hoxton.SR1</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
      <!--spring cloud alibaba 2.1.0.RELEASE-->
      <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-alibaba-dependencies</artifactId>
        <version>2.1.0.RELEASE</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
      <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>${mysql.version}</version>
      </dependency>
      <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>druid</artifactId>
        <version>${druid.version}</version>
      </dependency>
      <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
        <version>${mybatis.spring.boot.version}</version>
      </dependency>
      <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>${junit.version}</version>
      </dependency>
      <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>${lombok.version}</version>
        <optional>true</optional>
      </dependency>
    </dependencies>
  </dependencyManagement>

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

</project>

123456789101112131415161718192021222324252627282930313233343536373839404142434445464748495051525354555657585960616263646566676869707172737475767778798081828384858687888990919293949596979899100101102103104105106
```

## 三. 建立支付module: cloud-provider-payment8001

### 3.1 项目结构

![项目结构图](https://img-blog.csdnimg.cn/20200319115831628.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

### 3.2 建立子module的pom文件

- **dependencies**可以不用写version,会去父工程里找,写了的话就会用自己的版本号

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

    <artifactId>cloud-provider-payment8001</artifactId>
    
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
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.1.10</version>
        </dependency>
        <!--mysql-connector-java-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jdbc</artifactId>
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

1234567891011121314151617181920212223242526272829303132333435363738394041424344454647484950515253545556575859
```

### 3.3 子模块的yml配置文件

```yml
server:
  port: 8001

spring:
  application:
    name: cloud-provider-service
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource      #当前数据源操作类型
    driver-class-name: org.gjt.mm.mysql.Driver        #mysql驱动包
    url: jdbc:mysql://localhost:3306/db2019?useUnicode=true&characterEncoding-utr-8&useSSL=false
    username: root
    password: root

mybatis:
    mapper-locations: classpath:mapper/*.xml
    type-aliases-package: com.atguigu.springcloud.entities       #所有Entity别名类所在包
12345678910111213141516
```

### 3.4 建库

**库名db2019**

```sql
CREATE TABLE `payment`(
	`id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'ID',
	`serial` varchar(200) DEFAULT '',
	PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8

123456
```

### 3.5 业务类

#### 3.5.1 目录结构

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200319144536407.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 3.5.2 实体类

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Payment implements Serializable {

    private Long id;
    private String serial;

}

12345678910
```

**定义一个通用返回类**

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class CommonResult<T> {
    private Integer code;
    private String message;
    private  T      data;

    public CommonResult(Integer code,String message){
        this(code,message,null);
    }

}

1234567891011121314
```

#### 3.5.3 service及实现类

**目录结构**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200319154716664.png)
**PaymentService**

```java
package com.atguigu.springcloud.service;

import com.atguigu.springcloud.entities.Payment;
import org.apache.ibatis.annotations.Param;

public interface PaymentService {

    public int create(Payment payment);

    public Payment getPaymentById(@Param("id") Long id);
}

123456789101112
```

**PaymentServiceImpl**

```java
package com.atguigu.springcloud.service.impl;

import com.atguigu.springcloud.dao.PaymentDao;
import com.atguigu.springcloud.entities.Payment;
import com.atguigu.springcloud.service.PaymentService;
import org.springframework.stereotype.Service;

import javax.annotation.Resource;

@Service
public class PaymentServiceImpl implements PaymentService {

    @Resource
    private PaymentDao paymentDao;


    @Override
    public int create(Payment payment) {
        return  paymentDao.create(payment);
    }

    @Override
    public Payment getPaymentById(Long id) {
        return paymentDao.getPaymentById(id);
    }
}

123456789101112131415161718192021222324252627
```

#### 3.5.4 Controller实现

```java
package com.atguigu.springcloud.controller;

import com.atguigu.springcloud.entities.CommonResult;
import com.atguigu.springcloud.entities.Payment;
import com.atguigu.springcloud.service.PaymentService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;

@RestController
@Slf4j
public class PaymentController {

    @Resource
    private PaymentService paymentService;

    @PostMapping(value = "/payment/create")
    public CommonResult create(Payment payment){
        int result = paymentService.create(payment);
        log.info("*******插入结果为: "+result);
        if(result>0){
            return new CommonResult(200,"插入数据库成功",result);
        }else{
            return new CommonResult(444,"插入数据库失败",null);
        }
    }

    @GetMapping(value = "/payment/get/{id}")
    public CommonResult getPaymentById(@PathVariable("id") Long id){
        Payment paymentById = paymentService.getPaymentById(id);
        log.info("*********查找结果为: "+paymentById);
        if(paymentById!=null){
            return new CommonResult(200,"查找成功",paymentById);
        }else{
            return new CommonResult(444,"查找为空,查找ID为: "+id,null);
        }

    }


}

12345678910111213141516171819202122232425262728293031323334353637383940414243444546
```

#### 3.5.5 测试启动

先启动我们本地springboot项目

**测试查询**

打开**Postman**测试工具,输入测试url进行测试,本地我之前进行插入过一条数据
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200319163612245.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

**测试插入**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200319164457239.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**数据库插入成功**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200319164538899.png)

### 3.6 建一个模块的总结

**第一阶段的项目结构图**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200319165947280.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**建一个模块总结步骤**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200319170047684.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**业务类**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200319170735439.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

**下一章节** [Springcloud-Alibaba〖二〗](https://editor.csdn.net/md/?articleId=104990775)