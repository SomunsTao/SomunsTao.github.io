---
title: Springcloud-Alibaba 〖四〗actuator微服务信息完善  DiscoveryClient Eureka自我保护理论知识
date: 2020-04-04
tags: Springcloud-Alibaba
categories: Springcloud-Alibaba
keywords: spring
description: Springcloud-Alibaba
---



# Springcloud-Alibaba 〖四〗actuator微服务信息完善 添加 DiscoveryClient Eureka自我保护理论知识

![img](https://csdnimg.cn/release/phoenix/template/new_img/original.png)

[ktoking](https://me.csdn.net/kingtok) 2020-03-21 21:35:01 ![img](https://csdnimg.cn/release/phoenix/template/new_img/articleReadEyes.png) 517 ![img](https://csdnimg.cn/release/phoenix/template/new_img/tobarCollect.png) 收藏 2 ![img](https://csdnimg.cn/release/phoenix/template/new_img/planImg.png) 原力计划

分类专栏： [springcloud-Alibaba](https://blog.csdn.net/kingtok/category_9822291.html)

版权



### Springcloud-Alibaba 〖四〗

- - - [PS: github仓库[仓库地址](https://github.com/ktoking/springcloud-alibaba)项目都放到里面了](https://blog.csdn.net/kingtok/article/details/105016708#PS_githubhttpsgithubcomktokingspringcloudalibaba_1)

  - [一. actuator微服务信息完善](https://blog.csdn.net/kingtok/article/details/105016708#_actuator_2)

  - - [1.1 在8001与8002项目中分别添加](https://blog.csdn.net/kingtok/article/details/105016708#11_80018002_3)
    - [1.2 点开payment8001找到 /health 路径](https://blog.csdn.net/kingtok/article/details/105016708#12_payment8001_health__15)
    - [1.3 配置访问路径可以访问IP](https://blog.csdn.net/kingtok/article/details/105016708#13_IP_19)

  - [二. 添加 DiscoveryClient 发现微服务](https://blog.csdn.net/kingtok/article/details/105016708#__DiscoveryClient__24)

  - - [2.1 修改8001项目controller层](https://blog.csdn.net/kingtok/article/details/105016708#21_8001controller_25)
    - [2.2 调用该服务](https://blog.csdn.net/kingtok/article/details/105016708#22__93)

  - [三. Eureka自我保护理论知识](https://blog.csdn.net/kingtok/article/details/105016708#_Eureka_97)

  - - [3.1 关闭Eureka自我保护机制](https://blog.csdn.net/kingtok/article/details/105016708#31_Eureka_105)

  - [至此,Springcloud Eureka篇章结束~](https://blog.csdn.net/kingtok/article/details/105016708#Springcloud_Eureka_109)



### PS: github仓库[仓库地址](https://github.com/ktoking/springcloud-alibaba)项目都放到里面了

## 一. actuator微服务信息完善

### 1.1 在8001与8002项目中分别添加

```yml
instance:
    instance-id: payment8001
12
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321203438263.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

**改完后我们的自定义服务名称就暴露出来了**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321203543950.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

### 1.2 点开payment8001找到 /health 路径

**安全检查正常**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321203732481.png)

### 1.3 配置访问路径可以访问IP

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321203955191.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**当鼠标放到刚配置的主机名上时,左下角就会显示主机的IP**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321204135427.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

## 二. 添加 DiscoveryClient 发现微服务

### 2.1 修改8001项目controller层

```java
package com.atguigu.springcloud.controller;

import com.atguigu.springcloud.entities.CommonResult;
import com.atguigu.springcloud.entities.Payment;
import com.atguigu.springcloud.service.PaymentService;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.cloud.client.ServiceInstance;
import org.springframework.cloud.client.discovery.DiscoveryClient;
import org.springframework.web.bind.annotation.*;

import javax.annotation.Resource;
import java.util.List;

@RestController
@Slf4j
public class PaymentController {

    @Value("${server.port}")
    private String serverPort;

    @Resource
    private DiscoveryClient discoveryClient;

    @Resource
    private PaymentService paymentService;

    @PostMapping(value = "/payment/create")
    public CommonResult create(@RequestBody Payment payment){
        int result = paymentService.create(payment);
        log.info("*******插入结果为: "+result);
        if(result>0){
            return new CommonResult(200,"插入数据库成功"+"机器端口号为:"+serverPort,result);
        }else{
            return new CommonResult(444,"插入数据库失败"+"机器端口号为:"+serverPort,null);
        }
    }

    @GetMapping(value = "/payment/get/{id}")
    public CommonResult getPaymentById(@PathVariable("id") Long id){
        Payment paymentById = paymentService.getPaymentById(id);
        log.info("*********查找结果为: "+paymentById);
        if(paymentById!=null){
            return new CommonResult(200,"查找成功: "+"机器端口号为:"+serverPort,paymentById);
        }else{
            return new CommonResult(444,"查找为空,查找ID为: "+id+"机器端口号为:"+serverPort,null);
        }

    }

    @GetMapping(value = "/payment/discovery")
    public Object discovery(){
        List<String> services = discoveryClient.getServices();
        for (String service : services) {
           log.info("********服务名称*****"+service);
        }

        List<ServiceInstance> instances = discoveryClient.getInstances("CLOUD-PAYMENT-SERVICE");
        for (ServiceInstance instance : instances) {
            log.info(instance.getServiceId()+"\t"+instance.getHost()+"\t"+instance.getPort()+"\t"+instance.getUri());
        }
        return this.discoveryClient;
    }
}
12345678910111213141516171819202122232425262728293031323334353637383940414243444546474849505152535455565758596061626364
```

### 2.2 调用该服务

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321210702833.png)
**控制台打印出了相关信息**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321210854959.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

## 三. Eureka自我保护理论知识

**概述：** 保护模式主要用于一组客户端和Eureka Server之间存在网络分区场景下的保护，一旦进入保护模式，Eureka Server将会尝试保护其服务注册表中的信息，不再删除服务注册表中的数据，也就是不会注销任何微服务。
**一句话：** 某时刻某一个微服务不可用了，Eureka不会立刻清理，依旧会对该微服务的信息进行保存。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321211613651.png)

如果在Eureka Server的首页看到以上这段提示，则说明Eureka进入了保护模式。属于CAP里面的AP分支。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321211655637.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

### 3.1 关闭Eureka自我保护机制

**这里开启暴力剔除模式**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321212731532.png)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200321212959822.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

## 至此,Springcloud Eureka篇章结束~