---
title: Spring：Spring系列之bean的使用
date: 2020-3-22
tags: Spring
categories: Spring
keywords: spring
description: Spring
---



# ![img](http://java.tedu.cn/upload/20180306/20180306104159_146.jpg)

原文链接：https://www.cnblogs.com/xiaoxi/p/5850095.html

原作者：平凡希

------

------

# [Spring系列之bean的使用](https://www.cnblogs.com/xiaoxi/p/5850095.html)

**一、Bean的定义**

```
<bean id="userDao" class="com.dev.spring.simple.MemoryUserDao"/>
这是一个最简单的 Bean 定义。它类似于调用了语句：
MemoryUserDao userDao = new MemoryUserDao()。
```

id属性必须是一个有效的 XML ID,这意味着它在整个 XML 文档中必须唯一。它是一个 Bean 的“终身代号”。同时你也可以用 name 属性为 Bean 定义一个或多个别名（用逗号或空格分开多个别名）。name 属性允许出现任意非法的 XML 字母。例如：

```
<bean id="userDao" name="userDao*_1, userDao*_2" class="com.dev.spring.simple.MemoryUserDao"/>。
```

class属性定义了这个 Bean 的全限定类名（包名＋类名）。Spring 能管理几乎所有的 Java 类。一般情况，这个 Java 类会有一个默认的构造函数，用set方法设置依赖的属性。

Bean 元素出了上面的两个属性之外，还有很多其它属性。说明如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
<bean
    id="beanId"（1）
    name="beanName"（2）
    class="beanClass"（3）
    parent="parentBean"（4）
    abstract="true | false"（5）
    singleton="true | false"（6）
    lazy-init="true | false | default"（7）
    autowire="no | byName | byType | constructor | autodetect | default"（8）
    dependency-check = "none | objects | simple | all | default"（9）
    depends-on="dependsOnBean"（10）
    init-method="method"（11）
    destroy-method="method"（12）
    factory-method="method"（13）
    factory-bean="bean">（14）
</bean>
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 （1）id: Bean 的唯一标识名。它必须是合法的 XML ID，在整个 XML 文档中唯一。

（2）name: 用来为 id 创建一个或多个别名。它可以是任意的字母符合。多个别名之间用逗号或空格分开。

（3）class: 用来定义类的全限定名（包名＋类名）。只有子类 Bean 不用定义该属性。

（4）parent: 子类 Bean 定义它所引用它的父类 Bean。这时前面的 class 属性失效。子类 Bean 会继承父类 Bean 的所有属性，子类 Bean 也可以覆盖父类 Bean 的属性。注意：子类 Bean 和父类 Bean 是同一个 Java 类。

（5）abstract（默认为”false”）：用来定义 Bean 是否为抽象 Bean。它表示这个 Bean 将不会被实例化，一般用于父类 Bean，因为父类 Bean 主要是供子类 Bean 继承使用。

（6）singleton（默认为“true”）：定义 Bean 是否是 Singleton（单例）。如果设为“true”,则在 BeanFactory 作用范围内，只维护此 Bean 的一个实例。如果设为“flase”，Bean将是 Prototype（原型）状态，BeanFactory 将为每次 Bean 请求创建一个新的 Bean 实例。

（7）lazy-init（默认为“default”）：用来定义这个 Bean 是否实现懒初始化。如果为“true”，它将在 BeanFactory 启动时初始化所有的 Singleton Bean。反之，如果为“false”,它只在 Bean 请求时才开始创建 Singleton Bean。

（8）autowire（自动装配，默认为"default"）：它定义了 Bean 的自动装载方式。
"no"：不使用自动装配功能。
"byName"：通过 Bean 的属性名实现自动装配。
"byType"：通过 Bean 的类型实现自动装配。
"constructor"：类似于 byType，但它是用于构造函数的参数的自动组装。
"autodetect"：通过 Bean 类的反省机制（introspection）决定是使用“constructor”还是使用“byType”。

（9）dependency-check（依赖检查，默认为“default”）：它用来确保 Bean 组件通过 JavaBean 描述的所以依赖关系都得到满足。在与自动装配功能一起使用时，它特别有用。
none：不进行依赖检查。
objects：只做对象间依赖的检查。
simple：只做原始类型和 String 类型依赖的检查
all：对所有类型的依赖进行检查。它包括了前面的 objects 和 simple。

（10）depends-on（依赖对象）：这个 Bean 在初始化时依赖的对象，这个对象会在这个 Bean 初始化之前创建。

（11）init-method:用来定义 Bean 的初始化方法，它会在 Bean 组装之后调用。它必须是一个无参数的方法。

（12）destroy-method：用来定义 Bean 的销毁方法，它在 BeanFactory 关闭时调用。同样，它也必须是一个无参数的方法。它只能应用于singleton Bean。

（13）factory-method：定义创建该 Bean 对象的工厂方法。它用于下面的"factory-bean"，表示这个 Bean 是通过工厂方法创建。此时，"class"属性失效。

（14）factory-bean:定义创建该 Bean 对象的工厂类。如果使用了"factory-bean"则"class"属性失效。

**二、实例化Bean的三种方式**

**1.使用构造器实例化Bean**

这是最简单的方式，Spring IOC容器即能使用默认空构造器也能使用有参数构造器两种方式创建Bean。
使用空构造器进行定义，class属性指定的类必须有空构造器。使用有参数构造器进行定义，可以使用< constructor-arg >标签指定构造器参数值，其中index表示位置，value表示常量值，也可以指定引用，指定引用使用ref来引用另一个Bean定义，后边会详细介绍。下面来看一个例子：

（1）定义一个接口

```
package com.spring.service;

public interface IUserService {
    public void show();
}
```

 （2）实现类，该类有一个空构造器和一个有参构造器：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
package com.spring.service.impl;

import com.spring.service.IUserService;

public class UserServiceImpl implements IUserService{
    
    private String message;
    
    public UserServiceImpl(){
        this.message="lixiaoxi";
    }
    
    public UserServiceImpl(String message){
        this.message=message;
    }
    
    public void show(){
        System.out.println("hello,"+message);
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

（3）在配置文件（applicationContext1.xml）中配置Bean定义，如下所示：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
<!-- 使用默认构造函数 -->
<bean id="bean1" class="com.spring.service.impl.UserServiceImpl">  </bean>
    
<!-- 使用有参数构造函数 -->
<bean id="bean2" class="com.spring.service.impl.UserServiceImpl" >
    <!-- 指定构造器参数 -->
    <constructor-arg index="0" value="zhangsan" />
</bean>
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

（4）测试方法：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
/**
 * 使用构造器实例化Bean
 */
@Test
public void testCreateBeanByConstructor(){
    //读取配置文件
    ApplicationContext ctx=new ClassPathXmlApplicationContext("applicationContext1.xml");
    //获取bean的实例
    IUserService bean1=(IUserService) ctx.getBean("bean1");
    //调用方法
    bean1.show();
    
    IUserService bean2=(IUserService) ctx.getBean("bean2");
    bean2.show();
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

**2.使用静态工厂方法实例化Bean**

使用这种方式除了指定必须的class属性，还要指定factory-method属性来指定实例化Bean的方法，而且使用静态工厂方法也允许指定方法参数，spring IoC容器将调用此属性指定的方法来获取Bean。

（1）定义静态工厂类：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
package com.spring.factory;

import com.spring.service.IUserService;
import com.spring.service.impl.UserServiceImpl;

public class UserStaticFactory {
    //工厂方法
    public static IUserService newInstance(String message){
        //返回需要的Bean实例
        return new UserServiceImpl(message);
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

（2）在配置文件（applicationContext1.xml）中配置Bean定义，如下所示：

```
  <!-- 使用静态工厂方法 -->
  <bean id="bean3" class="com.spring.factory.UserStaticFactory" factory-method="newInstance" >
      <constructor-arg index="0" value="lisi" />
  </bean>
```

（3）测试方法：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
/**
 * 使用静态工厂实例化Bean
 */
@Test
public void testCreateBeanByStaticFactory(){
    ApplicationContext ctx=new ClassPathXmlApplicationContext("applicationContext1.xml");
    IUserService bean3=(IUserService) ctx.getBean("bean3");
    bean3.show();
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

**3.使用实例工厂方法实例化Bean**

使用这种方式不能指定class属性，此时必须使用factory-bean属性来指定工厂Bean，factory-method属性指定实例化Bean的方法，而且使用实例工厂方法允许指定方法参数，方式和使用构造器方式一样，配置如下：

（1）定义实例工厂类：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
package com.spring.factory;

import com.spring.service.IUserService;
import com.spring.service.impl.UserServiceImpl;

public class UserInstanceFactory {
    
    public IUserService newInstance(String message){
        return new UserServiceImpl(message);
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

（2）在配置文件（applicationContext1.xml）中配置Bean定义，如下所示：

```
  <!-- 1.定义实例工厂Bean -->
  <bean id="beanInstanceFactory" class="com.spring.factory.UserInstanceFactory" />
  <!-- 2.使用实例工厂Bean创建Bean -->
  <bean id="bean4" factory-bean="beanInstanceFactory" factory-method="newInstance" >
       <constructor-arg index="0" value="aaaa"></constructor-arg>
  </bean> 
```

（3）测试方法：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
/**
 * 使用实例工厂实例化Bean
 */
@Test
public void testCreateBeanByInstanceFactory(){
    ApplicationContext ctx=new ClassPathXmlApplicationContext("applicationContext1.xml");
    IUserService bean4=(IUserService) ctx.getBean("bean4");
    bean4.show();
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

**总结：**

这三种方式只是配置不一样，从获取方式看完全一样，没有任何不同。这也是Spring IoC的魅力，Spring IoC帮你创建Bean，我们只管使用就可以了，是不是很简单。

**三、Bean的作用域**

 什么是作用域呢？即“scope”，在面向对象程序设计中一般指对象或变量之间的可见范围。而在Spring容器中是指其创建的Bean对象相对于其他Bean对象的请求可见范围。
Spring提供“singleton”和“prototype”两种基本作用域，另外提供“request”、“session”、“globalsession”三种web作用域；Spring还允许用户定制自己的作用域。

| 作用域        | 描述                                                         |
| ------------- | ------------------------------------------------------------ |
| singleton     | 在每个Spring IoC容器中一个bean定义对应一个对象实例。（默认）在spring IOC容器中仅存在一个Bean实例,Bean以单实例的方式存在。 |
| prototype     | 一个bean定义对应多个对象实例。每次从容器中调用Bean时,都返回一个新的实例,即每次调用getBean()时,相当于执行new XxxBean()的操作。 |
| request       | 在一次HTTP请求中，一个bean定义对应一个实例；即每次HTTP请求将会有各自的bean实例，它们依据某个bean定义创建而成。该作用域仅在基于web的Spring `ApplicationContext`情形下有效。 |
| session       | 在一个HTTP `Session`中，一个bean定义对应一个实例。该作用域仅在基于web的Spring `ApplicationContext`情形下有效。同一个HTTP session共享一个Bean,不同HTTP session使用不同的Bean,该作用域仅适用于webApplicationContext环境。 |
| globalSession | 在一个全局的HTTP `Session`中，一个bean定义对应一个实例。典型情况下，仅在使用portlet context的时候有效。该作用域仅在基于web的Spring `ApplicationContext`情形下有效。 |

 **1.singleton**
“singleton”作用域的Bean只会在每个Spring IoC容器中存在一个实例，而且其完整生命周期完全由Spring容器管理。对于所有获取该Bean的操作Spring容器将只返回同一个Bean。注意:spring将Bean的默认作用域定为singleton。

当一个bean的作用域设置为singleton, 那么Spring IOC容器中只会存在一个共享的bean实例，并且所有对bean的请求，只要id与该bean定义相匹配，则只会返回bean的同一实例。换言之，当把 一个bean定义设置为singleton作用域时，Spring IOC容器只会创建该bean定义的唯一实例。这个单一实例会被存储到单例缓存（singleton cache）中，并且所有针对该bean的后续请求和引用都将返回被缓存的对象实例，这里要注意的是singleton作用域和GOF设计模式中的单例是完全不同的，单例设计模式表示一个ClassLoader中只有一个class存在，而这里的singleton则表示一个容器对应一个bean，也就是说当一个bean被标识为singleton时候，spring的IOC容器中只会存在一个该bean。

在默认情况下,spring的ApplicationContext容器在启动时,自动实例化所有singleton的Bean并缓存于容器中。
虽然启动时会花费一些时间,但带来两个好处:
(1)首先对Bean提前的实例化操作会及早发现一些潜在的配置问题。
(2)其次Bean以缓存的方式保存,当运行时使用到该Bean时就无须再实例化了,加快了运行效率。
如果用户不希望在容器启动时提前实例化singleton的Bean,可以通过lazy-init属性进行控制。但是lazy-init="true"的Bean在某些情况下依旧会提前实例化:如果该Bean被其它需要提前实例化的Bean引用到,spring也将忽略延迟实例化的设置。

**2.prototype**

即原型，指每次向Spring容器请求获取Bean都返回一个全新的Bean，相对于"singleton"来说就是不缓存Bean，每次都是一个根据Bean定义创建的全新Bean。

当使用prorotype作为作用域时，Bean会导致每次对该Bean的请求都创建一个Bean实例，所以对有状态的Bean应该使用prorotype作用域，无状态Bean 则使用singleton作用域。还有就是Spring不能对一个prototype作用域 bean的整个生命周期负责，容器在初始化、配置、装饰或者是装配完一个prototype实例后，将它交给客户端，随后就对该prototype实例不闻不问了。

在默认情况下,spring容器在启动时不实例化prototype的Bean。此外,spring容器将prototype的Bean交给调用者后,就不再管理它的生命周期。

下面测试一下singleton与prototype，Java类用之前建的HelloWorld.java。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
package com.spring.test;

public class HelloWorld {
    private String info;

    public String getInfo() {
        return info;
    }

    public void setInfo(String info) {
        this.info = info;
    }
    
    public HelloWorld(){
        System.out.println("执行构造函数！");
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

配置文件applicationContext.xml：

```
<bean id="hello" class="com.spring.test.HelloWorld">  
    <property name="info" value="Hello,This is my first Spring Application!"></property>  
</bean> 
```

其中id为"hello"的bean声明为singleton（因为默认是singleton，所以可以不显示指定）
测试方法如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
/**
 * 测试Bean的作用域
 */
@Test
public void test(){
    //读取配置文件
    ApplicationContext ctx=new ClassPathXmlApplicationContext("applicationContext.xml");
    //获取bean的实例
    HelloWorld t=(HelloWorld) ctx.getBean("hello");
    HelloWorld t1=(HelloWorld) ctx.getBean("hello");
    System.out.println(t==t1);
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

执行结果为：

![img](https://images2015.cnblogs.com/blog/249993/201609/249993-20160909140752551-653373142.png)

可以看到只打印了一次“执行构造函数！”，并且t=t1，说明它们是同一对象。

修改配置文件，将id为"hello"的bean的scope属性改为"prototype"。

```
<bean id="hello" class="com.spring.test.HelloWorld" scope="prototype">  
    <property name="info" value="Hello,This is my first Spring Application!"></property>  
</bean> 
```

再次执行上面的测试方法，结果如下：

![img](https://images2015.cnblogs.com/blog/249993/201609/249993-20160909141543238-1852638183.png)

打印了两次“执行构造函数！”，并且t!=t1。

**3.web应用中的作用域**

在Web应用中，我们可能需要将数据存储到request、session、global session。因此Spring提供了三种Web作用域：request、session、globalSession。

**（1）request作用域**：**request表示针对每一次HTTP请求都会产生一个新的bean，同时该bean仅在当前HTTP request内有效。**示例：

```
<bean id="loginAction" class="com.foo.LoginAction" scope="request"/>
```

 针对每次HTTP请求，Spring容器会根据loginAction bean定义创建一个全新的LoginAction bean实例， 且该loginAction bean实例仅在当前HTTP request内有效，因此可以根据需要放心的更改所建实例的内部状态， 而其他请求中根据loginAction bean定义创建的实例，将不会看到这些特定于某个请求的状态变化。 当处理请求结束，request作用域的bean实例将被销毁。

**（2）session作用域：针对每个会话，spring容器都会创建一个全新的Bean，且该Bean仅在当前HTTP Session内有效。**

```
<bean id="userPreferences" class="com.foo.UserPreferences" scope="session"/>
```

针对某个HTTP `Session`，Spring容器会根据`userPreferences` bean定义创建一个全新的`userPreferences` bean实例， 且该`userPreferences` bean仅在当前HTTP `Session`内有效。 与`request作用域`一样，你可以根据需要放心的更改所创建实例的内部状态，而别的HTTP `Session`中根据`userPreferences`创建的实例，将不会看到这些特定于某个HTTP `Session`的状态变化。 当HTTP `Session`最终被废弃的时候，在该HTTP `Session`作用域内的bean也会被废弃掉。

**（3）globalSession作用域：类似于session作用域，只是其用于portlet环境的web应用。如果在非portlet环境将视为session作用域。**

```
<bean id="userPreferences" class="com.foo.UserPreferences" scope="globalSession"/>
```

`global session`作用域类似于标准的HTTP `Session`作用域，不过它仅仅在基于portlet的web应用中才有意义。Portlet规范定义了全局`Session`的概念，它被所有构成某个portlet web应用的各种不同的portlet所共享。在`global session`作用域中定义的bean被限定于全局portlet `Session`的生命周期范围内。

请注意，假如你在编写一个标准的基于Servlet的web应用，并且定义了一个或多个具有`global session`作用域的bean，系统会使用标准的HTTP `Session`作用域，并且不会引起任何错误。