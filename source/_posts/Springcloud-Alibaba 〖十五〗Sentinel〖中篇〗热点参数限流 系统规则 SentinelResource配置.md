---
title: Springcloud-Alibaba 〖十五〗Sentinel〖中篇〗热点参数限流 系统规则 SentinelResource配置
date: 2020-04-15
tags: Springcloud-Alibaba 
categories: Springcloud-Alibaba 
keywords: spring
description: Springcloud-Alibaba 
---



# Springcloud-Alibaba 〖十五〗Sentinel〖中篇〗热点参数限流 系统规则 SentinelResource配置



### Springcloud-Alibaba 〖十五〗Sentinel 热点参数限流 系统规则 SentinelResource配置

- - - [PS: github仓库[仓库地址](https://github.com/ktoking/springcloud-alibaba)项目都放到里面了](https://blog.csdn.net/kingtok/article/details/105642215#PS_githubhttpsgithubcomktokingspringcloudalibaba_1)

    - [一. 热点参数限流](https://blog.csdn.net/kingtok/article/details/105642215#__3)

    - [二. 热点规则配置](https://blog.csdn.net/kingtok/article/details/105642215#__12)

    - - [2.1 8401项目新增方法](https://blog.csdn.net/kingtok/article/details/105642215#21_8401_13)
      - [2.2 Sentinel配置热点规则](https://blog.csdn.net/kingtok/article/details/105642215#22__Sentinel_29)
      - [2.3 测试](https://blog.csdn.net/kingtok/article/details/105642215#23__32)
      - [2.4 热点规则高级配置](https://blog.csdn.net/kingtok/article/details/105642215#24__39)

    - [三. 系统规则](https://blog.csdn.net/kingtok/article/details/105642215#__49)

    - - [3.1 参数列表](https://blog.csdn.net/kingtok/article/details/105642215#31__54)

    - [四. SentinelResource配置](https://blog.csdn.net/kingtok/article/details/105642215#_SentinelResource_66)

    - - [4.1 8401项目新增依赖](https://blog.csdn.net/kingtok/article/details/105642215#41_8401_68)
      - [4.2 新增Controller RateLimitController](https://blog.csdn.net/kingtok/article/details/105642215#42_Controller_RateLimitController_78)
      - [4.3 新增限流规则](https://blog.csdn.net/kingtok/article/details/105642215#43__107)
      - [4.4 测试](https://blog.csdn.net/kingtok/article/details/105642215#44__109)
      - [4.5 新增Url方法](https://blog.csdn.net/kingtok/article/details/105642215#45_Url_113)
      - [4.6 出现的问题](https://blog.csdn.net/kingtok/article/details/105642215#46__128)
      - [4.7 全局的兜底方法](https://blog.csdn.net/kingtok/article/details/105642215#47__130)
      - [4.8 核心定义](https://blog.csdn.net/kingtok/article/details/105642215#48__167)



### PS: github仓库[仓库地址](https://github.com/ktoking/springcloud-alibaba)项目都放到里面了

[Springcloud-Alibaba官网文档](https://github.com/alibaba/Sentinel/wiki/热点参数限流)

### 一. 热点参数限流

**何为热点？热点即经常访问的数据。很多时候我们希望统计某个热点数据中访问频次最高的 Top K 数据，并对其访问进行限制。比如：**

- 商品 ID 为参数，统计一段时间内最常购买的商品 ID 并进行限制
- 用户 ID 为参数，针对一段时间内频繁访问的用户 ID 进行限制

热点参数限流会统计传入参数中的热点参数，并根据配置的限流阈值与模式，对包含热点参数的资源调用进行限流。热点参数限流可以看做是一种特殊的流量控制，仅对包含热点参数的资源调用生效。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200420190709413.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

### 二. 热点规则配置

#### 2.1 8401项目新增方法

```java
	@GetMapping("/testHotKey")
    @SentinelResource(value = "testHotKey",blockHandler = "deal_testHostkey")
    public String testHotKey(@RequestParam(value = "p1",required = false) String p1,
                             @RequestParam(value = "p2",required = false) String p2) {
        return "-----testHotKey";
    }

    public String deal_testHostkey(String p1, String p2, BlockException e){
        return "-----deal_testHotKey";
    }
12345678910
```

- **SentinelResource注解的 value 表示注册到Sentinel的资源名称**
- **blockHandler 默认的兜底规则,表示出错了会返回我们自己写的方法中,其中我们自己写的方法中要增加一个BlockException 参数**

#### 2.2 Sentinel配置热点规则

**当我们第一个参数访问次数大于1QPS我们会抛出我们自己的兜底方法**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200420192647384.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 2.3 测试

**当我们不超过规则时**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200420192819178.png)
**当触发热点规则时(携带的参数为p1时),会返回我们自己定义的兜底方法,看起来是不是非常克莱弯呢~**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200420192813296.png)**但是我们没有携带指定参数p1时可以随便访问**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200420193230885.png)

#### 2.4 热点规则高级配置

**我们期望p1参数当它是某个特殊值时，它的限流值和平时不一样**

就是充钱了~我VIP不得是人上人吗?(暗示自己)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200420193745776.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**测试,我们把参数设置为5随便点都不会限流(QPS<=200)**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200420193813807.png)

**PS小贴士~**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200420194128743.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

### 三. 系统规则

官网复制机~~

**Sentinel 系统自适应限流从整体维度对应用入口流量进行控制，结合应用的 Load、CPU 使用率、总体平均 RT、入口 QPS 和并发线程数等几个维度的监控指标，通过自适应的流控策略，让系统的入口流量和系统的负载达到一个平衡，让系统尽可能跑在最大吞吐量的同时保证系统整体的稳定性。**

#### 3.1 参数列表

**系统规则支持以下的模式**：

- **Load 自适应（**仅对 Linux/Unix-like 机器生效）：系统的 load1 作为启发指标，进行自适应系统保护。当系统 load1 超过设定的启发值，且系统当前的并发线程数超过估算的系统容量时才会触发系统保护（BBR 阶段）。系统容量由系统的 maxQps * minRt 估算得出。设定参考值一般是 CPU cores * 2.5。
- **CPU usage**（1.5.0+ 版本）：当系统 CPU 使用率超过阈值即触发系统保护（取值范围 0.0-1.0），比较灵敏。
- **平均 RT**：当单台机器上所有入口流量的平均 RT 达到阈值即触发系统保护，单位是毫秒。
- **并发线程数**：当单台机器上所有入口流量的并发线程数达到阈值即触发系统保护。
- **入口 QPS**：当单台机器上所有入口流量的 QPS 达到阈值即触发系统保护。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200420195313988.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

### 四. SentinelResource配置

#### 4.1 8401项目新增依赖

**这个是之前的公共类**

```java
		<dependency>
            <groupId>com.aiguigu.springcloud</groupId>
            <artifactId>cloud-api-commons</artifactId>
            <version>1.0-SNAPSHOT</version>
            <scope>compile</scope>
        </dependency>
123456
```

#### 4.2 新增Controller RateLimitController

```java
package com.atguigu.springcloud.controller;

import com.alibaba.csp.sentinel.annotation.SentinelResource;
import com.alibaba.csp.sentinel.slots.block.BlockException;
import com.atguigu.springcloud.entities.CommonResult;
import com.atguigu.springcloud.entities.Payment;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class RateLimitController {

    @GetMapping("/byResource" )
    @SentinelResource(value = "byResource" , blockHandler = "handleException")
    public CommonResult byResource(){
        return new CommonResult(  200,  "按资源名称限流测试oK" ,new Payment(  2020L, "serial001"));
    }
    public CommonResult handleException(BlockException exception){
        return new CommonResult(  444,  exception.getClass().getCanonicalName()+"\t  服务不可用");
    }
}



123456789101112131415161718192021222324
```

#### 4.3 新增限流规则

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200420200833452.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 4.4 测试

**但是服务重启后,规则就消失了,所以这个是一个临时规则**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200420201031942.png)

#### 4.5 新增Url方法

**Controller层新增Url方法**

```java
@GetMapping("/rateLimit/byUrl" )
    @SentinelResource(value = "byUr1")
    public CommonResult byUrl(){
        return new CommonResult(  200, "按ur1限流测试oK",new Payment( 2020L,  "seria1002"));
    }
12345
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200420201451113.png)
**没配置的时候是默认的规则方法**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200420201721425.png)

#### 4.6 出现的问题

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200420202010924.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 4.7 全局的兜底方法

**controller新增方法**

```java
  @GetMapping("/rateLimit/customerBlockHandler" )
    @SentinelResource(value = "customerBlockHandler",
                      blockHandlerClass = CustomerBlockHandler.class,blockHandler="handlerException2")
    public CommonResult customerBlockHandler(){
        return new CommonResult(  200, "按客户自定义",new Payment( 2020L,  "seria1003"));
    }
123456
```

**新增全局兜底类(注意方法必须是static的)**

```java
package com.atguigu.springcloud.handler;

import com.alibaba.csp.sentinel.slots.block.BlockException;
import com.atguigu.springcloud.entities.CommonResult;
import com.atguigu.springcloud.entities.Payment;

public class CustomerBlockHandler {

    public static CommonResult handlerException(BlockException e){
        return new CommonResult(  444, "按客户自定义,global handlerException-------1 ");
    }

    public static CommonResult handlerException2(BlockException e){
        return new CommonResult(  444, "按客户自定义,global handlerException-------2 ");
    }
}

1234567891011121314151617
```

**这里我们要切记**
![在这里插入图片描述](https://img-blog.csdnimg.cn/2020042020454438.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

**测试**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200420204315372.png)

#### 4.8 核心定义

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200420205053584.png)