---
title: Springcloud-Alibaba 〖十四〗Sentinel〖上篇〗 流控 降级
date: 2020-04-14
tags: Springcloud-Alibaba 
categories: Springcloud-Alibaba 
keywords: spring
description: Springcloud-Alibaba 
---

# Springcloud-Alibaba 〖十四〗Sentinel〖上篇〗 流控 降级



### Springcloud-Alibaba 〖十四〗Sentinel

- - - [PS: github仓库[仓库地址](https://github.com/ktoking/springcloud-alibaba)项目都放到里面了](https://blog.csdn.net/kingtok/article/details/105400023#PS_githubhttpsgithubcomktokingspringcloudalibaba_1)

    - [一. Sentinel是什么?](https://blog.csdn.net/kingtok/article/details/105400023#_Sentinel_6)

    - [二. 安装使用](https://blog.csdn.net/kingtok/article/details/105400023#__22)

    - [三. 新建模块 cloudalibaba-sentinel-service8401](https://blog.csdn.net/kingtok/article/details/105400023#__cloudalibabasentinelservice8401_33)

    - - [3.1 项目结构](https://blog.csdn.net/kingtok/article/details/105400023#31__34)
      - [3.2 pom](https://blog.csdn.net/kingtok/article/details/105400023#32_pom_36)
      - [3.3 application.yml](https://blog.csdn.net/kingtok/article/details/105400023#33_applicationyml_102)
      - [3.4 主启动类](https://blog.csdn.net/kingtok/article/details/105400023#34__132)
      - [3.5 业务类](https://blog.csdn.net/kingtok/article/details/105400023#35__151)
      - [3.6 测试](https://blog.csdn.net/kingtok/article/details/105400023#36__175)

    - [四. 流控规则](https://blog.csdn.net/kingtok/article/details/105400023#__184)

    - - [4.1 流控规则介绍](https://blog.csdn.net/kingtok/article/details/105400023#41__185)
      - [4.2 直接 快速失败](https://blog.csdn.net/kingtok/article/details/105400023#42___209)
      - [4.3 关联 快速失败](https://blog.csdn.net/kingtok/article/details/105400023#43___218)
      - [4.4 流控效果——Warm Up](https://blog.csdn.net/kingtok/article/details/105400023#44_Warm_Up_233)
      - [4.5 流控效果——排队等待](https://blog.csdn.net/kingtok/article/details/105400023#45__239)

    - [五. 降级规则](https://blog.csdn.net/kingtok/article/details/105400023#__253)

    - - [5.1 RT](https://blog.csdn.net/kingtok/article/details/105400023#51_RT_261)
      - [5.2 异常比例](https://blog.csdn.net/kingtok/article/details/105400023#52__287)
      - [5.3 异常数](https://blog.csdn.net/kingtok/article/details/105400023#53__292)

    - [不说了,准备实习面试了~可能会放慢更新速度555](https://blog.csdn.net/kingtok/article/details/105400023#555_298)

    - [下期来一波总结面试问题,奥里给,干了兄弟们](https://blog.csdn.net/kingtok/article/details/105400023#_299)

  - [转载请标注~](https://blog.csdn.net/kingtok/article/details/105400023#_300)



### PS: github仓库[仓库地址](https://github.com/ktoking/springcloud-alibaba)项目都放到里面了

**官网地址: [Sentinel](https://github.com/alibaba/Sentinel)**

**百度云下载地址: [Sentinel-DashBoard1.7.0](https://pan.baidu.com/s/1JGJJlOIqVkTxNf0L1ueJAg)** 提取码：gkaq

### 一. Sentinel是什么?

**Sentinel: 分布式系统的流量防卫兵**

**Sentinel 具有以下特征:**

- **丰富的应用场景**: Sentinel 承接了阿里巴巴近 10 年的双十一大促流量的核心场景，例如秒杀（即突发流量控制在系统容量可以承受的范围）、消息削峰填谷、集群流量控制、实时熔断下游不可用应用等。
- **完备的实时监控**：Sentinel 同时提供实时的监控功能。您可以在控制台中看到接入应用的单台机器秒级数据，甚至 500 台以下规模的集群的汇总运行情况。
- **广泛的开源生态**：Sentinel 提供开箱即用的与其它开源框架/库的整合模块，例如与 Spring Cloud、Dubbo、gRPC 的整合。您只需要引入相应的依赖并进行简单的配置即可快速地接入 Sentinel。
  完善的 SPI 扩展点：Sentinel 提供简单易用、完善的 SPI 扩展接口。您可以通过实现扩展接口来快速地定制逻辑。例如定制规则管理、适配动态数据源等。
- **丰富的应用场景**：Sentinel 承接了阿里巴巴近 10 年的双十一大促流量的核心场景，例如秒杀（即突发流量控制在系统容量可以承受的范围）、消息削峰填谷、集群流量控制、实时熔断下游不可用应用等。
  完备的实时监控：Sentinel 同时提供实时的监控功能。您可以在控制台中看到接入应用的单台机器秒级数据，甚至 500 台以下规模的集群的汇总运行情况。
- **广泛的开源生态**：Sentinel 提供开箱即用的与其它开源框架/库的整合模块，例如与 Spring Cloud、Dubbo、gRPC 的整合。您只需要引入相应的依赖并进行简单的配置即可快速地接入 Sentinel。
  完善的 SPI 扩展点：Sentinel 提供简单易用、完善的 SPI 扩展接口。您可以通过实现扩展接口来快速地定制逻辑。例如定制规则管理、适配动态数据源等。
  ![在这里插入图片描述](https://img-blog.csdnimg.cn/20200408231236451.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

### 二. 安装使用

**运行jar包,sentinel默认端口8080,可以指定端口,我这里指定8085端口**
`java -jar sentinel-dashboard-1.7.0.jar --server.port=8085`![在这里插入图片描述](https://img-blog.csdnimg.cn/20200409105030859.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

**登录控制台**

默认密码和用户都是sentinel
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200409105050160.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**这里就登录上去了**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200409105145661.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

### 三. 新建模块 cloudalibaba-sentinel-service8401

#### 3.1 项目结构

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200409111024592.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

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

    <artifactId>cloudalibaba-sentinel-service8401</artifactId>

    <!--pom.xml依赖-->
    <dependencies>
        <!--springcloud alibaba nacos-->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
        <!--springcloud alibaba sentinel-datasource-nacos 后续做持久化用到-->
        <dependency>
            <groupId>com.alibaba.csp</groupId>
            <artifactId>sentinel-datasource-nacos</artifactId>
        </dependency>
        <!--springcloud alibaba sentinel-->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
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
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>com.aiguigu.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>1.0-SNAPSHOT</version>
            <scope>compile</scope>
        </dependency>
    </dependencies>

</project>
12345678910111213141516171819202122232425262728293031323334353637383940414243444546474849505152535455565758596061
```

#### 3.3 application.yml

```yml
#yml配置
server:
  port: 8401

spring:
  application:
    name: cloudalibaba-sentinel-service
  cloud:
    nacos:
      discovery:
        #Nacos服务注册中心地址
        server-addr: localhost:8848
    sentinel:
      transport:
        #配置sentinel dashboard地址
        dashboard: localhost:8085
        #默认8719端口，假如被占用会自动从8719开始依次+1扫描，直至找到未被占用的端口
        port: 8719

management:
  endpoints:
    web:
      exposure:
        include: '*'

12345678910111213141516171819202122232425
```

#### 3.4 主启动类

```java
package com.atguigu.springcloud;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

@SpringBootApplication
@EnableDiscoveryClient
public class MainApp8401 {
    public static void main(String[] args) {
        SpringApplication.run(MainApp8401.class,args);
    }
}

1234567891011121314
```

#### 3.5 业务类

```java
package com.atguigu.springcloud.controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;


@RestController
public class FlowLimitController {

    @GetMapping("/testA")
    public String testA(){
        return "--------testA";
    }

    @GetMapping("/testB")
    public String testB(){
        return "--------testB";
    }
}

12345678910111213141516171819
```

#### 3.6 测试

**首先测试方法,可以访问到**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200409111614420.png)
**这时候来到Nacos界面,发现服务已经注册进来**
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020040911160395.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**最后来到sentinel界面,由于sentinel是懒加载机制,这时我们多访问几次服务,发现有如下界面**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200409111552959.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**这样就完成了sentinel的基本使用**

### 四. 流控规则

#### 4.1 流控规则介绍

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200409112549151.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

- **资源名**：唯一名称，默认请求路径
- **针对来源**：Sentinel可以针对调用者进行限流，填写微服务名，默认default（不区分来源）
- **阈值类型/单机阈值**：

1. QPS（每秒钟的请求数量）：当调用该api的QPS达到阈值的时候，进行限流
2. 线程数：当调用该api的线程数达到阈值的时候，进行限流

- **是否集群**：（不）需要集群
- **流控模式**：

1. 直接：api达到限流条件时，直接限流
2. 关联：当关联的资源达到阈值时，就限流自己
3. 链路：只记录指定链路上的流量（指定资源从入口资源进来的流量，如果达到阈值，就进行限流）

- **流控效果**：

1. 快速失败：直接失败，抛异常
2. Warm Up：根据codeFactor（冷加载因子，默认3）的值，从阈值/codeFactor，经过预热时长，才达到设置的QPS阈值。
3. 排队等待：匀速排队，让请求以匀速的速度通过，阈值类型必须设置为QPS。否则无效。

#### 4.2 直接 快速失败

**我们来新增一个规则,1s只能有1次访问**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200409113329846.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
表示1秒钟内查询1次就是OK,若超过次数1,就直接快速失败，报默认错误

**失败结果**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200409113434248.png)

#### 4.3 关联 快速失败

**适合做应用的让步**

- 当关联的资源达到阈值时，就限流自己
- 当与A关联的资源B达到阀值后，就限流A自己

**设置关联资源/testB**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200409150738153.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

**postman测试**

这里我们起20个线程每隔0.3s访问一次
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200409151409459.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**可以看到/testB访问满了后,A服务直接挂了**
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020040915153885.png)

#### 4.4 流控效果——Warm Up

Warm Up方式，即**预热/冷启动方式**。当系统长期处于低水位的情况下，当流量突然增加时，直接把系统拉升到高水位可能瞬间把系统压垮。通过“冷启动”，让通过的流量缓慢增加，在一定时间内逐渐增加到阈值上限，给冷系统一个预热的时间，避免冷系统被压垮。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200409161618113.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
默认**coldFactor**为**3**，即请求QPS从threshold / 3开始，经预热时长逐渐升至设定的QPS阈值。
秒杀系统在开启的瞬间，会有很多流量进来，很有可能把系统打死，预热方式就是为了保护系统，可慢慢的把流量放进来，慢慢的把阈值增长到设置的阈值。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200409162240788.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 4.5 流控效果——排队等待

**匀速排队**,让请求以均匀的速度通过,阀值类型必须设成QPS,否则无效。
设置含义: /testA每秒1次请求，超过的话就排队等待，等待的超时时间为20000毫秒。

这种方式:主要用于处理间隔性突发的流量，例如消息队列。想象一下这样的场景， 在某一秒有大量的请求到来，而接下来的几秒则处于空闲状态，我们希望系统能够在接下来的空闲期间逐渐处理这些请求，而不是在第一秒直接拒绝多余的请求。

**修改我们testB方法,打印当前线程日志**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200409165234853.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**sentinel规则设置**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200409165607682.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**并发测试**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200409165549982.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200409165750728.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

### 五. 降级规则

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020040916592182.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

------

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200409170255198.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

- Sentinel熔断降级会在调用链路中某个资源出现不稳定状态时(例如调用超时或异常比例升高)，对这个资源的调用进行限制,让请求快速失败，避免影响到其它的资源而导致级联错误。
- 当资源被降级后，在接下来的降级时间窗口之内，对该资源的调用都自动熔断(默认行为是抛出DegradeException)。

#### 5.1 RT

**controller层新增方法**

```java
 @GetMapping("/testD")
    public String testD() {
        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return "----- testD";
    }
123456789
```

**返回时间要是超过200毫秒,就会在1s内降级**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200409192916483.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**测试**

这里每秒发10个线程过去
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200409193204570.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**再次访问我们的服务的时候,服务已经报错了**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200409193736793.png)
永远一秒钟打进来10个线程(**大于5个了**)调用testD, 我们希望200毫秒处理完本次任务

如果超过200毫秒还没处理完，在未来1秒钟的时间窗口内，断路器打开(保险丝跳闸)微服务不可用，保险丝跳闸断电了后续我停止jmeter,没有这么大的访问量了，断路器关闭(保险丝恢复),微服务恢复OK

#### 5.2 异常比例

**首先资源量要>=5**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200409194353474.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
后台继续用Jmeter发请求,结果如下
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200409194736845.png)

#### 5.3 异常数

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200409195121309.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200409195352264.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**这里设置异常数大于5时,时间窗口设置70s显示**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200409195441977.png)

### 不说了,准备实习面试了~可能会放慢更新速度555

### 下期来一波总结面试问题,奥里给,干了兄弟们

## 转载请标注~