---
title: SpringBoot：小总结
date: 2020-3-30
tags: SpringBoot
categories: SpringBoot
keywords: spring
description: you can "just run"
---



![img](http://p99.pstatp.com/large/pgc-image/15367619528862cca2a0df9)

# [spring boot 总结](https://www.cnblogs.com/huangjianping/p/8203811.html)

**一、什么是SpringBoot**
　　描述：Spring Boot是Spring社区发布的一个开源项目，旨在帮助开发者快速并且更简单的构建项目。大多数SpringBoot项目只需要很少的配置文件。
**二、SpringBoot核心功能**
1、独立运行Spring项目
　　　　Spring boot 可以以jar包形式独立运行，运行一个Spring Boot项目只需要通过java -jar xx.jar来运行。
2、内嵌servlet容器
Spring Boot可以选择内嵌Tomcat、jetty或者Undertow,这样我们无须以war包形式部署项目。
3、提供starter简化Maven配置
spring提供了一系列的start pom来简化Maven的依赖加载，例如，当你使用了spring-boot-starter-web，会自动加入如图5-1所示的依赖包。
4、自动装配Spring
SpringBoot会根据在类路径中的jar包，类、为jar包里面的类自动配置Bean，这样会极大地减少我们要使用的配置。当然，SpringBoot只考虑大多数的开发场景，并不是所有的场景，若在实际开发中我们需要配置Bean，而SpringBoot灭有提供支持，则可以自定义自动配置。
5、准生产的应用监控
SpringBoot提供基于http ssh telnet对运行时的项目进行监控。
6、无代码生产和xml配置　　
SpringBoot不是借助与代码生成来实现的，而是通过条件注解来实现的，这是Spring4.x提供的新特性。
**三、SpringBoot优缺点**
优点：
1、快速构建项目。
2、对主流开发框架的无配置集成。
3、项目可独立运行，无须外部依赖Servlet容器。
4、提供运行时的应用监控。
5、极大的提高了开发、部署效率。
6、与云计算的天然集成。
缺点：
1、如果你不认同spring框架，也许这就是缺点。
**四、SpringBoot特性**
1、创建独立的Spring项目
2、内置Tomcat和Jetty容器
3、提供一个starter POMs来简化Maven配置
4、提供了一系列大型项目中常见的非功能性特性，如安全、指标，健康检测、外部配置等
5、完全没有代码生成和xml配置文件
**五、SpringBoot快速搭建**
网址：http://start.spring.io;
**六、SpringBoot CLI**
SpringBoot CLI 是SpringBoot提供的控制台命令工具。
**七、SpringBoot maven 构建项目**
spring-boot-starter-parent：是一个特殊Start，它用来提供相关的Maven依赖项，使用它之后，常用的包依赖可以省去version标签。
**八、SpringBoot几个常用的注解**
（1）@RestController和@Controller指定一个类，作为控制器的注解 
（2）@RequestMapping方法级别的映射注解，这一个用过Spring MVC的小伙伴相信都很熟悉 
（3）@EnableAutoConfiguration和@SpringBootApplication是类级别的注解，根据maven依赖的jar来自动猜测完成正确的spring的对应配置，只要引入了spring-boot-starter-web的依赖，默认会自动配置Spring MVC和tomcat容器 
（4）@Configuration类级别的注解，一般这个注解，我们用来标识main方法所在的类,完成元数据bean的初始化。 
（5）@ComponentScan类级别的注解，自动扫描加载所有的Spring组件包括Bean注入，一般用在main方法所在的类上 
（6）@ImportResource类级别注解，当我们必须使用一个xml的配置时，使用@ImportResource和@Configuration来标识这个文件资源的类。 
（7）@Autowired注解，一般结合@ComponentScan注解，来自动注入一个Service或Dao级别的Bean 
（8）@Component类级别注解，用来标识一个组件，比如我自定了一个filter，则需要此注解标识之后，Spring Boot才会正确识别。

 