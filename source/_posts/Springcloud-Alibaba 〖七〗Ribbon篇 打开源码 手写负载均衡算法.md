---
title: Springcloud-Alibaba 〖七〗Ribbon篇 打开源码 手写负载均衡算法
date: 2020-04-07
tags: Springcloud-Alibaba 
categories: Springcloud-Alibaba 
keywords: spring
description: Springcloud-Alibaba 
---



# Springcloud-Alibaba 〖七〗Ribbon篇 打开源码 手写负载均衡算法



### Springcloud-Alibaba 〖七〗Ribbon篇

- - - [PS: github仓库[仓库地址](https://github.com/ktoking/springcloud-alibaba)项目都放到里面了](https://blog.csdn.net/kingtok/article/details/105049480#PS_githubhttpsgithubcomktokingspringcloudalibaba_1)

    - [一. Ribbon 是什么?](https://blog.csdn.net/kingtok/article/details/105049480#_Ribbon__2)

    - [二. LB负载均衡（Load Balance）](https://blog.csdn.net/kingtok/article/details/105049480#__LBLoad_Balance_8)

    - [三. Ribbon架构](https://blog.csdn.net/kingtok/article/details/105049480#_Ribbon_19)

    - [四. RestTemplate调用](https://blog.csdn.net/kingtok/article/details/105049480#_RestTemplate_24)

    - - [4.1 getForObject()方法](https://blog.csdn.net/kingtok/article/details/105049480#41_getForObject_25)
      - [4.2 getForObject()方法](https://blog.csdn.net/kingtok/article/details/105049480#42_getForObject_45)

    - [五. Ribbon的负载均衡机制 IRule](https://blog.csdn.net/kingtok/article/details/105049480#_Ribbon_IRule_65)

    - - [5.1 目录结构](https://blog.csdn.net/kingtok/article/details/105049480#51__68)
      - [5.2 创建规则类](https://blog.csdn.net/kingtok/article/details/105049480#52__71)
      - [5.3 主启动类添加注解](https://blog.csdn.net/kingtok/article/details/105049480#53__92)
      - [5.4 测试](https://blog.csdn.net/kingtok/article/details/105049480#54__98)

    - [六. 负载均衡算法](https://blog.csdn.net/kingtok/article/details/105049480#__102)

    - - [6.1 负载均衡算法： 轮询](https://blog.csdn.net/kingtok/article/details/105049480#61____104)
      - [6.2 接口类](https://blog.csdn.net/kingtok/article/details/105049480#62__128)
      - [6.3 Ribbon源码](https://blog.csdn.net/kingtok/article/details/105049480#63_Ribbon_131)

    - [七. 手写负载均衡算法](https://blog.csdn.net/kingtok/article/details/105049480#__242)

    - - [7.1 改controller](https://blog.csdn.net/kingtok/article/details/105049480#71_controller_243)
      - [7.2 80项目注掉@LoadBalanced](https://blog.csdn.net/kingtok/article/details/105049480#72_80LoadBalanced_263)
      - [7.3 80项目增加一个接口和一个实现类](https://blog.csdn.net/kingtok/article/details/105049480#73_80_267)
      - [7.4 controller层](https://blog.csdn.net/kingtok/article/details/105049480#74_controller_324)
      - [7.5 测试](https://blog.csdn.net/kingtok/article/details/105049480#75__349)

    - [终于肝完了Ribbon,说实话写这篇的时间真的很长,因为基础真的很差,所以会去补一些基础,看完可别白嫖哦~](https://blog.csdn.net/kingtok/article/details/105049480#Ribbon_358)

    - [转载请标注!](https://blog.csdn.net/kingtok/article/details/105049480#_359)



### PS: github仓库[仓库地址](https://github.com/ktoking/springcloud-alibaba)项目都放到里面了

### 一. Ribbon 是什么?

**Spring Cloud Ribbon 是基于Netflix Ribbon 实现的一套客户端 负载均衡的工具。**

Ribbon 是 Netflix 发布的开源项目，主要功能是提供**客户端**的软件**负载均衡**算法和**服务调用**。Ribbon 客户端组件提供一系列完善的配置项如连接超时，重试等。简单的说，就是在配置文件中列出 Load Balancer（简称LB）后面所有的机器，Ribbon 会自动的帮助你基于某种规则（如简单轮询、随机连接等）去连接这些机器。我们很容易使用Ribbon实现自定义的负载均衡算法。

### 二. LB负载均衡（Load Balance）

简单的说就是将用户的请求平摊的分配到多个服务上，从而达到系统的HA（高可用）。常见的负载均衡有软件 Nginx，LVS，硬件F5 等。

- **集中式B**
  即在服务的消费方和提供方之间使用独立的LB设施（可以是硬件，如F5，也可以是软件，如nginx）,由该设施负责把访问请求通过某种策略转发至服务的提供方
- **进程内LB**
  将 LB 逻辑集成到消费方，消费方从服务注册中心获知有哪些地址可用，然后自己再从这些地址中选择出一个合适的服务器。Ribbon就属于进程内 LB ，它只是一个类库，集成与消费方进程，消费方通过它来获取到服务提供方的地址。

**Ribbon 就是 负载均衡 + RestTemplate调用，最终实现RPC的远程调用。**

### 三. Ribbon架构

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020032315265393.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**由于eureka天生集成了ribbon,所以可以不用添加依赖就可以用ribbon**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200323153142367.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

### 四. RestTemplate调用

#### 4.1 getForObject()方法

```java
    @GetMapping("/consumer/payment/getEntity/{id}")
    public CommonResult<Payment> getForEntity(@PathVariable("id") Long id){
        ResponseEntity<CommonResult> forEntity = restTemplate.getForEntity(PAYMENT_URL + "/payment/get/" + id, CommonResult.class);
        if ((forEntity.getStatusCode().is2xxSuccessful())){
            return forEntity.getBody();
        }else {
            return new CommonResult<>(444,"调用失败");
        }
    }

1234567891011
```

**getForObject可以获取返回的更多信息,包括请求头,请求状态码,等等**.
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200323154907257.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**测试**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200323154745584.png)

#### 4.2 getForObject()方法

- **getForObject()其实比getForEntity()多包含了将HTTP转成POJO的功能，但是getForObject没有处理response的能力。因为它拿到手的就是成型的pojo。省略了很多response的信息。**

**调用**

```java
@GetMapping("/consumer/payment/create")
    public CommonResult<Payment> create(Payment payment){
        log.info("*******消费者启动创建订单*******");
        return restTemplate.postForObject(PAYMENT_URL+"/payment/create",payment,CommonResult.class);
    }

    @GetMapping("/consumer/payment/get/{id}")
    public CommonResult<Payment> getPayment(@PathVariable("id") Long id){
        return restTemplate.getForObject(PAYMENT_URL+"/payment/get/"+id,CommonResult.class);
    }
12345678910
```

**测试结果**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200323160340916.png)

### 五. Ribbon的负载均衡机制 IRule

**默认采用轮询机制**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200323160944159.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 5.1 目录结构

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200323162059928.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 5.2 创建规则类

**这个自定义配置类不能放在 @ComponentScan 所扫描的当前包下以及子包下，否则自定义的配置类就会被所有的 Ribbon 客户端所共享，达不到特殊化定制的目的了。所以我们在java目录下新建 com.atguigu.myrule.MyselfRule类,这里我们创建出随机规则**

```java
package com.atguigu.myrule;

import com.netflix.loadbalancer.IRule;
import com.netflix.loadbalancer.RandomRule;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class MyselfRule {

    @Bean
    public IRule myRule(){
        return new RandomRule(); //定义为随机
    }
}

12345678910111213141516
```

#### 5.3 主启动类添加注解

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200323162517391.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

```java
@RibbonClient(name = "CLOUD-PAYMENT-SERVICE",configuration = MyselfRule.class)
1
```

#### 5.4 测试

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200323162606304.png)
**在之前轮询的情况下端口是8001与8002交替出现,而负载均衡规则变为随机后,端口是随机出现的**

### 六. 负载均衡算法

#### 6.1 负载均衡算法： 轮询

- **rest 接口第几次请求数 % 服务器集群总数量 = 实际调用服务器位置下标**
- **每次服务器重启后rest接口数从1开始**

*List instances = discoveryClient.getInstances(“CLOUD-PROVIDER-SERVICE”)*

1. List[0] instances = 127.0.0.1:8002
2. List[1] instances = 127.0.0.1:8001

**例如我们现在有两台机子去负载均衡**

| 请求次数 | 计算公式 | 取得下标           |
| -------- | -------- | ------------------ |
| 1        | 1%2=1    | 对应127.0.0.1:8001 |
| 2        | 2%2=0    | 对应127.0.0.1:8002 |
| 3        | 3%2=1    | 对应127.0.0.1:8001 |
| …        | …        | …                  |

#### 6.2 接口类

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200323190521134.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 6.3 Ribbon源码

**IRule接口**

```java
//IRule接口
public interface IRule{
    /*
     * choose one alive server from lb.allServers or
     * lb.upServers according to key
     * 
     * @return choosen Server object. NULL is returned if none
     *  server is available 
     */
	//选择哪个服务实例
    public Server choose(Object key);
    
    public void setLoadBalancer(ILoadBalancer lb);
    
    public ILoadBalancer getLoadBalancer();    
}

1234567891011121314151617
```

**RoundRobinRule 轮询源码**

```java
public class RoundRobinRule extends AbstractLoadBalancerRule {

    private AtomicInteger nextServerCyclicCounter;
    private static final boolean AVAILABLE_ONLY_SERVERS = true;
    private static final boolean ALL_SERVERS = false;

    private static Logger log = LoggerFactory.getLogger(RoundRobinRule.class);

    public RoundRobinRule() {
        nextServerCyclicCounter = new AtomicInteger(0);
    }

    public RoundRobinRule(ILoadBalancer lb) {
        this();
        setLoadBalancer(lb);
    }

    public Server choose(ILoadBalancer lb, Object key) {
        if (lb == null) {
            log.warn("no load balancer");
            return null;
        }

        Server server = null;
        int count = 0;
        while (server == null && count++ < 10) {
            List<Server> reachableServers = lb.getReachableServers();
            List<Server> allServers = lb.getAllServers();
            int upCount = reachableServers.size();
            int serverCount = allServers.size();

            if ((upCount == 0) || (serverCount == 0)) {
                log.warn("No up servers available from load balancer: " + lb);
                return null;
            }

            int nextServerIndex = incrementAndGetModulo(serverCount);
            server = allServers.get(nextServerIndex);

            if (server == null) {
                /* Transient. */
                Thread.yield();
                continue;
            }

            if (server.isAlive() && (server.isReadyToServe())) {
                return (server);
            }

            // Next.
            server = null;
        }

        if (count >= 10) {
            log.warn("No available alive servers after 10 tries from load balancer: "
                    + lb);
        }
        return server;
    }

    /**
     * Inspired by the implementation of {@link AtomicInteger#incrementAndGet()}.
     *
     * @param modulo The modulo to bound the value of the counter.
     * @return The next value.
     */
    private int incrementAndGetModulo(int modulo) {
        for (;;) {
            int current = nextServerCyclicCounter.get();
            int next = (current + 1) % modulo;
            if (nextServerCyclicCounter.compareAndSet(current, next))
                return next;
        }
    }

    @Override
    public Server choose(Object key) {
        return choose(getLoadBalancer(), key);
    }

    @Override
    public void initWithNiwsConfig(IClientConfig clientConfig) {
    }
}

12345678910111213141516171819202122232425262728293031323334353637383940414243444546474849505152535455565758596061626364656667686970717273747576777879808182838485
```

**这里用了点CAS知识点,不会的小伙伴们可以移步这里:[什么是CAS](https://blog.csdn.net/kingtok/article/details/105058823)**

### 七. 手写负载均衡算法

#### 7.1 改controller

**在8001项目端增加一个方法**

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200324195708515.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

```java
   @GetMapping("/payment/lb")
    public String getPaymentLB(){
        return serverPort;
    }
1234
```

**在8002项目端同样增加一个方法**

```java
  @GetMapping("/payment/lb")
    public String getPaymentLB(){
        return serverPort;
    }
1234
```

#### 7.2 80项目注掉@LoadBalanced

**注释掉,毕竟我们要用自己写的**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200324195849899.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

#### 7.3 80项目增加一个接口和一个实现类

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200324195957621.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)
**接口**

```java
package com.aiguigu.springcloud.lb;

import org.springframework.cloud.client.ServiceInstance;

import java.util.List;

public interface LoadBalancer {

    ServiceInstance instances(List<ServiceInstance> serviceInstances);
}

1234567891011
```

**实现类**

```java
package com.aiguigu.springcloud.lb;

import org.springframework.cloud.client.ServiceInstance;
import org.springframework.cloud.client.loadbalancer.LoadBalanced;
import org.springframework.stereotype.Component;

import java.lang.annotation.Annotation;
import java.util.List;
import java.util.concurrent.atomic.AtomicInteger;

@Component
public class MyLB implements LoadBalancer {

    private AtomicInteger atomicInteger=new AtomicInteger(0);

    public final int getAndIncrement(){
        int current;
        int next;
        do{
            current=atomicInteger.get();
            next=current>=2147483647?0:current+1;
        }while (!this.atomicInteger.compareAndSet(current,next));
        System.out.println("***第几次访问,次数: "+next);
        return next;
    }


    public ServiceInstance instances(List<ServiceInstance> serviceInstances){
        int index=getAndIncrement()%serviceInstances.size();
        return serviceInstances.get(index);
    }
}

123456789101112131415161718192021222324252627282930313233
```

- **这里首先有一个原子类int型,初始值为0,这里用了一个自旋锁,让他判断每次是不是我们之前的那个值,如果是就+1代表访问次数又增加一次,不是就继续循环直到判断为真跳出循环,这里保证了不用synchronized方法也能在高并发下实现线程安全的增加次数**
- **第二个方法instances()实现了用当前访问次数去%一个集群的数量,使这个值永远不超过集群数量,然后得到这个值作为获取单个实例的下标,返回一个当前应该返回的集群实例**

#### 7.4 controller层

**新增方法getPaymentLB()**

```java
 	@Resource
    private LoadBalancer loadBalancer;

    @Resource
    private DiscoveryClient discoveryClient;
    
    @GetMapping(value = "/consumer/payment/lb")
    public  String getPaymentLB(){
        List<ServiceInstance> instances = discoveryClient.getInstances("CLOUD-PAYMENT-SERVICE");
        if(instances==null||instances.size()<0){
            return null;
        }
        ServiceInstance instances1 = loadBalancer.instances(instances);
        URI uri = instances1.getUri();
        return restTemplate.getForObject(uri+"/payment/lb",String.class);
    }

1234567891011121314151617
```

**首先获取集群中的实例,然后判断是否为空,把获取到的list集群传给刚写的方法中获取到负载均衡获取到的当前实例,然后获取到实例地址,最后restTemplate去调用服务就会用到我们之前写的负载均衡去调用**

#### 7.5 测试

**调用方法会一直轮询调用,体现了我们刚才的负载均衡机制**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200324194944646.png)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200324194934667.png)
**服务端控制台也打印出了第几次访问,服务重启后会次数会变为0,所以保证内存不溢出,刚才设置的最大值为int的最大值**
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200324194954897.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tpbmd0b2s=,size_16,color_FFFFFF,t_70)

### 终于肝完了Ribbon,说实话写这篇的时间真的很长,因为基础真的很差,所以会去补一些基础,看完可别白嫖哦~

### 转载请标注!