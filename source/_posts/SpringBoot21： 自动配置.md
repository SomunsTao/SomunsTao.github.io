---
title: SpringBoot：自动配置解析
date: 2020-5-3
tags: SpringBoot
categories: SpringBoot
keywords: spring
description: you can "just run"
---



![img](http://p99.pstatp.com/large/pgc-image/15367619528862cca2a0df9)

原文链接：https://www.cnblogs.com/leihuazhe/p/7743479.html



------

------

> SpringBoot 自动配置主要通过 `@EnableAutoConfiguration`, `@Conditional`, `@EnableConfigurationProperties` 或者 `@ConfigurationProperties` 等几个注解来进行自动配置完成的。
>
> `@EnableAutoConfiguration` 开启自动配置，主要作用就是调用 `Spring-Core` 包里的 `loadFactoryNames()`，将 `autoconfig` 包里的已经写好的自动配置加载进来。
>
> `@Conditional` 条件注解，通过判断类路径下有没有相应配置的 `jar` 包来确定是否加载和自动配置这个类。
>
> `@EnableConfigurationProperties` 的作用就是，给自动配置提供具体的配置参数，只需要写在 `application.properties` 中，就可以通过映射写入配置类的 `POJO` 属性中。

### @EnableAutoConfiguration

@Enable*注释并不是`SpringBoot`新发明的注释，Spring 3框架就引入了这些注释，用这些注释替代XML配置文件。比如：
`@EnableTransactionManagement`注释，它能够声明事务管理
`@EnableWebMvc`注释，它能启用Spring MVC
`@EnableScheduling`注释，它可以初始化一个调度器。

#### **这些注释事实上都是简单的配置，通过`@Import`注释导入**。

##### 从启动类的@SpringBootApplication进入，在里面找到了@EnableAutoConfiguration,

![1.png](http://pic.hzways.com/1.png)

![2.png](http://pic.hzways.com/2.png)

##### @EnableAutoConfiguration里通过@Import导入了`EnableAutoConfigurationImportSelector`,

![3.png](http://pic.hzways.com/3.png)

##### 进入他的父类`AutoConfigurationImportSelector`

![4.png](http://pic.hzways.com/4.png)

##### 找到`selectImports()`方法，他调用了`getCandidateConfigurations()`方法，在这里，这个方法又调用了Spring Core包中的`loadFactoryNames()`方法。这个方法的作用是，会查询`META-INF/spring.factories`文件中包含的`JAR`文件。

![5.png](http://pic.hzways.com/5.png)

##### 当找到spring.factories文件后，SpringFactoriesLoader将查询配置文件命名的属性。

![6.png](http://pic.hzways.com/6.png)

![7.png](http://pic.hzways.com/7.png)

##### `Jar`文件在`org.springframework.boot.autoconfigure的spring.factories`

![8.png](http://pic.hzways.com/8.png)

##### `spring.factories`内容如下(截取部分),在这个文件中，可以看到一系列Spring Boot自动配置的列表

```properties
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\
org.springframework.boot.autoconfigure.batch.BatchAutoConfiguration,\
org.springframework.boot.autoconfigure.cache.CacheAutoConfiguration,\
org.springframework.boot.autoconfigure.cassandra.CassandraAutoConfiguration,\
org.springframework.boot.autoconfigure.cloud.CloudAutoConfiguration,\
org.springframework.boot.autoconfigure.context.ConfigurationPropertiesAutoConfiguration,\
org.springframework.boot.autoconfigure.context.MessageSourceAutoConfiguration,\
org.springframework.boot.autoconfigure.context.PropertyPlaceholderAutoConfiguration,\
org.springframework.boot.autoconfigure.couchbase.CouchbaseAutoConfiguration,\
org.springframework.boot.autoconfigure.dao.PersistenceExceptionTranslationAutoConfiguration,\
org.springframework.boot.autoconfigure.data.cassandra.CassandraDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.cassandra.CassandraRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.couchbase.CouchbaseDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.couchbase.CouchbaseRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchAutoConfiguration,\
org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.jpa.JpaRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.ldap.LdapDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.ldap.LdapRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.mongo.MongoDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.mongo.MongoRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.neo4j.Neo4jDataAutoConfiguration,\
org.springframework.boot.autoconfigure.data.neo4j.Neo4jRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.solr.SolrRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.redis.RedisAutoConfiguration,\
org.springframework.boot.autoconfigure.data.redis.RedisRepositoriesAutoConfiguration,\
org.springframework.boot.autoconfigure.data.rest.RepositoryRestMvcAutoConfiguration,\
org.springframework.boot.autoconfigure.data.web.SpringDataWebAutoConfiguration,\
```

### 下面我们来看自动配置redis的细节，RedisAutoConfiguration：

### RedisAutoConfiguration

![redis.png](http://pic.hzways.com/redis.png)

这个类进行了简单的Spring配置，声明了Redis所需典型Bean，和其它很多类一样，重度依赖于Spring Boot注释：
1）@ConditionOnClass激活一个配置，当类路径中存在这个类时才会配置该类
2）@EnableConfigurationProperties自动映射一个POJO到Spring Boot配置文件（默认是application.properties文件）的属性集。
3）@ConditionalOnMissingBean启用一个Bean定义，但必须是这个Bean之前未定义过才有效。
还可以使用@ AutoConfigureBefore注释、@AutoConfigureAfter注释来定义这些配置类的载入顺序。

#### 着重了解@Conditional注释，Spring 4框架的新特性

此注释使得只有在特定条件满足时才启用一些配置。SrpingBoot的AutoConfig大量使用了@Conditional，它会根据运行环境来动态注入Bean。这里介绍一些@Conditional的使用和原理，并自定义@Conditional来自定义功能。

- @Conditional是SpringFramework的功能，SpringBoot在它的基础上定义了
- @ConditionalOnClass，@ConditionalOnProperty等一系列的注解来实现更丰富的内容。

#### 具体几个@Conditon*注解的含义

###### @ConditionalOnBean

仅仅在当前上下文中存在某个对象时，才会实例化一个Bean

###### @ConditionalOnClass

某个class位于类路径上，才会实例化一个Bean)，该注解的参数对应的类必须存在，否则不解析该注解修饰的配置类

###### @ConditionalOnExpression

当表达式为true的时候，才会实例化一个Bean

###### @ConditionalOnMissingBean

仅仅在当前上下文中不存在某个对象时，才会实例化一个Bean，该注解表示，如果存在它修饰的类的bean，则不需要再创建这个bean，可以给该注解传入参数例如@ConditionOnMissingBean(name = "example")，这个表示如果name为“example”的bean存在，这该注解修饰的代码块不执行

###### @ConditionalOnMissingClass

某个class类路径上不存在的时候，才会实例化一个Bean

###### @ConditionalOnNotWebApplication

不是web应用时，才会执行

------

### 2.Properties系列注释

@EnableConfigurationProperties
@ConfigurationProperties(prefix = "may")

在需要注入配置的类上加上这个注解，prefix的意思是，以该前缀打头的配置，以下是例子

```java
    @ConfigurationProperties(prefix = "may")  
    public class User {  
        private String name;  
        private String gender;  
        
       //省略setter,getter方法
      
    }  
```

application.yml中的配置

```
   may
      name: youjie
      gender: man
```

如果不用系统初始的application.yml配置类，而是使用自己的如youjie.yml，可以如下配置

```java
    @ConfigurationProperties(prefix = "may",locations = "classpath:youjie.yml")  
    public class User2 {  
        private String name;  
        private String gender;  
        
       //省略setter,getter方法
      
    }
  
```

过时：由于Spring-boot 1.5.2版本移除了，locations这个属性,因此上述这种方式在最新的版本中过时。
@PropertySource

Spring-boot 1.5.2版本之后，采用下面这种方式

```java
@Component
//@PropertySource只能加载.properties文件，需要将上面的yml文件，改为.properties文件
@PropertySource("classpath:may.properties")
@ConfigurationProperties(prefix="may") 
public class User2 {  
        private String name;  
        private String gender;  
        
       //省略setter,getter方法
      
    }  
```

@EnableConfigurationProperties

最后注意在spring Boot入口类加上@EnableConfigurationProperties

```java
    @SpringBootApplication  
    @EnableConfigurationProperties({User.class,User2.class})  
    public class DemoApplication {  
      
        public static void main(String[] args) {  
            SpringApplication.run(DemoApplication.class, args);  
        }  
    }  
```

其实这里@EnableConfigurationProperties({User.class,User2.class}) 可以省略

![config.png](http://pic.hzways.com/config.png)

### 总结

SpringBoot 的 自动配置得益于 SpringFramework 强大的支撑，框架早已有很多工具和注解可以自动装配 Bean 。SpringBoot 通过 一个封装，将市面上通用的组件直接写好了配置类。当我们程序去依赖了这些组件的 jar 包后，启动 SpringBoot应用，于是自动加载开始了。

我们也可以定义自己的自动装配组件，依赖之后，Spring直接可以加载我们定义的 starter 。笔者将在后续文章中进行编码和解读。