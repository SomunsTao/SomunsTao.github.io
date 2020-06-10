---
title: Spring：Spring系列之依赖之入方式
date: 2020-3-24
tags: Spring
categories: Spring
keywords: spring
description: Spring
---



![img](http://java.tedu.cn/upload/20180306/20180306104159_146.jpg)

原文链接：https://www.cnblogs.com/xiaoxi/p/5846416.html

原作者：平凡希

------

------

# [Spring系列之依赖注入的方式](https://www.cnblogs.com/xiaoxi/p/5865330.html)

**一、依赖注入方式**

   对于spring配置一个bean时，如果需要给该bean提供一些初始化参数，则需要通过依赖注入方式，所谓的依赖注入就是通过spring将bean所需要的一些参数传递到bean实例对象的过程，spring的依赖注入有3种方式：

**·使用属性的setter方法注入 ，这是最常用的方式；**
**·使用构造器注入；**
**·使用Filed注入（用于注解方式）。**

**1.使用属性注入**

   属性注入即通过setXxx()方法注入Bean的属性值或依赖对象，由于属性注入方式具有可选择性和灵活性高的优点，因此属性注入是实际应用中最常采用的注入方式。

```
<bean id=”……” class=”……”>  
    <property name=”属性1” value=”……”/>  
    <property name=”属性2” value=”……”/>  
    ……  
</bean>  
```

   属性注入要求Bean提供一个默认的构造函数，并为需要注入的属性提供对应的Setter方法。Spring先调用Bean的默认构造函数实例化Bean对象，然后通过反射的方式调用Setter方法注入属性值。来看一个简单的例子。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
package com.spring.model;

public class Car {
    
    private int maxSpeed;
    private String brand;
    private double price;
    
    public int getMaxSpeed() {
        return maxSpeed;
    }
    //一定要写被注入对象的set方法
    public void setMaxSpeed(int maxSpeed) {
        this.maxSpeed = maxSpeed;
    }
    public String getBrand() {
        return brand;
    }
    public void setBrand(String brand) {
        this.brand = brand;
    }
    public double getPrice() {
        return price;
    }
    public void setPrice(double price) {
        this.price = price;
    }
    
    public void run(){
        System.out.println("brand:"+brand+",maxSpeed:"+maxSpeed+",price:"+price);
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

   Car类中定义了3个属性，并分别提供了对应的Setter方法。（注：默认构造函数是不带参的构造函数。Java语言规定如果类中没有定义任何构造函数，则JVM自动为其生成一个默认的构造函数。反之，如果类中显示定义了构造函数，则JVM不会为其生成默认的构造函数。所以假设Car类中显示定义了一个带参的构造函数，如public Car(String brand)，则需要同时提供一个默认构造函数public Car()，否则使用属性注入时将抛出异常。）
下面在Spring配置文件中对Car进行属性注入：

```
<!-- 属性注入 -->
<bean id="car" class="com.spring.model.Car">  
    <property name="maxSpeed" value="200"></property>
    <property name="brand" value="红旗CA72"></property>  
    <property name="price" value="200000.00"></property>
</bean>
```

   在上述代码中配置了一个Bean，并为该Bean的3个属性提供了属性值。具体来说，Bean的每一个属性对应一个<property>标签，name为属性的名称，在Bean实现类中拥有与其对应的Setter方法：maxSpeed对应setMaxSpeed()，brand对应setBrand()。
   需要指出的是：Spring只会检查Bean中是否有对应的Setter方法，至于Bean中是否有对应的属性变量则不做要求。例如配置文件中<property name="brand"/>的属性配置项仅要求Car类中拥有setBrand()方法，但Car类不一定要拥有brand成员变量。

测试方法：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
/**
 * 属性注入
 */
@Test
public void test(){
    //读取配置文件
    ApplicationContext ctx=new ClassPathXmlApplicationContext("applicationContext.xml");
    //获取bean的实例
    Car car=(Car) ctx.getBean("car");
    car.run();
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 **2.构造函数注入**

构造函数注入是除属性注入之外的另一种常用的注入方式，它保证一些必要的属性在Bean实例化时就得到设置，并且确保了Bean实例在实例化后就可以使用。
**使用方式：**
第一，在类中，不用为属性设置setter方法，但是需要生成该类带参的构造方法。
第二，在配置文件中配置该类的bean，并配置构造器，在配置构造器中用到了<constructor-arg>节点，该节点有四个属性：
· index是索引，指定注入的属性，从0开始；
· type是指该属性所对应的类型；
· ref 是指引用的依赖对象；
· value 当注入的不是依赖对象，而是基本数据类型时，就用value；

**（1）按类型匹配入参**

   如果任何可用的Car对象都必须提供maxSpeed、brand和price的值，使用属性注入方式只能人为在配置时提供保证，而无法在语法级提供保证，这时通过构造函数注入就可以很好地满足这一要求。使用构造函数注入的前提是Bean必须提供带参的构造函数，下面为Car提供一个可设置maxSpeed、brand和price属性的构造函数。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
package com.spring.model;

public class Car {
    
    private int maxSpeed;
    private String brand;
    private double price;
    
    //带参构造方法
    public Car(int maxSpeed,String brand, double price){
        this.maxSpeed=maxSpeed;
        this.brand=brand;
        this.price=price;
    }
    
    public void run(){
        System.out.println("brand:"+brand+",maxSpeed:"+maxSpeed+",price:"+price);
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

构造函数注入的配置方式和属性注入方式的配置有所不同，在spring配置文件中使用构造函数注入装配这个Car Bean。

```
<!-- 构造函数注入(按类型匹配) -->
<bean id="car1" class="com.spring.model.Car">  
    <constructor-arg type="int" value="300"></constructor-arg>
    <constructor-arg type="java.lang.String" value="宝马"></constructor-arg>
    <constructor-arg type="double" value="300000.00"></constructor-arg>
</bean>
```

在<constructor-arg>的元素中有一个type属性，它表示构造函数中参数的类型，为spring提供了判断配置项和构造函数入参对应关系的“信息”。

**（2）按索引匹配入参**

   我们知道，Java语言通过入参的类型及顺序区分不同的重载方法，对于上面代码中的Car类，Spring仅通过type属性指定的参数类型就可以知道“宝马”对应String类型的brand入参，而“300000.00”对应double类型的price入参。但是，如果Car构造函数3个入参的类型相同，仅通过type就无法确定对应关系了，这时需要通过入参索引的方式进行确定。
为了更好地演示按索引匹配入参的配置方式，特意对Car构造函数进行一下调整。

```
public Car(String brand, String corp,double price){
    this.brand=brand;
    this.corp=corp;
    this.price=price;
}
```

brand和corp的入参类型都是String，所以String将无法确定type为String的<constructor-arg>到底对应的是brand还是corp。但是，通过显示指定参数的索引能够消除这种不确定性，如下所示。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
<!-- 构造函数注入(按索引匹配) -->
<bean id="car2" class="com.spring.model.Car"> 
    <!-- 注意索引从0开始 --> 
    <constructor-arg index="0" value="宝马"></constructor-arg>
    <constructor-arg index="1" value="中国一汽"></constructor-arg>
    <constructor-arg index="2" value="300000.00"></constructor-arg>
</bean>
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

构造函数第一个参数索引为0，第二个为1，以此类推，因此很容易知道“宝马”对应brand入参，而“中国一汽”对应corp入参。

**（3）联合使用类型和索引匹配入参**
   有时需要联合使用type和index才能确定匹配项和构造函数入参的对应关系，看下面的代码。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
public Car(String brand, String corp,double price){
    this.brand=brand;
    this.corp=corp;
    this.price=price;
}

public Car(String brand, String corp,int maxSpeed){
    this.brand=brand;
    this.corp=corp;
    this.maxSpeed=maxSpeed;
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

   这里，Car拥有两个重载的构造函数，它们都有三个入参。针对这种情况，按照入参索引的配置方式又难以满足要求了，这时需要联合使用<constructor-arg>的type和index才能解决问题，看下面代码。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
<!-- 构造函数注入(通过入参类型和位置索引确定对应关系) -->
<!-- 对应public Car(String brand, String corp,int maxSpeed)构造函数 -->
<bean id="car3" class="com.spring.model.Car">  
    <constructor-arg index="0" type="java.lang.String" value="奔驰"></constructor-arg>
    <constructor-arg index="1" type="java.lang.String" value="中国一汽"></constructor-arg>
    <constructor-arg index="2" type="int" value="200"></constructor-arg>
</bean>
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

   对于上面的两个构造函数，如果仅通过index进行配置，Spring将无法确定第3个入参配置项究竟是对应int的maxSpeed还是double的price，采用索引匹配时，真正引起歧义的地方在于第3个入参，因此仅需要明确指定第3个入参的类型就可以取消歧义了。所以在上面的代码中，第1个和第2个<constructor-arg>元素的type属性可以去除。
   对于由于参数数目相同而类型不同所引起的潜在配置歧义问题，Spring容器可以正确启动且不会给出报错信息，它将随机采用一个匹配的构造函数实例化Bean，而被选择的构造函数可能并不是用户所希望的。因此，必须特别谨慎，以避免潜在的错误。

 **3.使用字段（Filed）注入（用于注解方式）**

除了上面讲到的使用属性的setter方法或使用构造器方法来注入依赖对象，还有一种注入依赖对象的方法，就是使用注解。

来看一个例子，首先不使用注解的方式。

新建一个业务接口：

```
package com.spring.service;

public interface ICommonService {
    
    public void add();
}
```

实现类：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
package com.spring.service.impl;

import com.spring.dao.ICommonDao;
import com.spring.service.ICommonService;

public class CommonServiceImpl implements ICommonService{
    
    private ICommonDao commonDao;
    
    // 依赖注入DAO组件所需的setter方法
    public void setCommonDao(ICommonDao commonDao) {
        this.commonDao = commonDao;
    }
    
    public void add(){
        commonDao.add();
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

dao层接口：

```
package com.spring.dao;

public interface ICommonDao {
    public void add();
}
```

实现类：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
package com.spring.dao.impl;

import com.spring.dao.ICommonDao;

public class CommonDaoImpl implements ICommonDao{
    
    public void add(){
        System.out.println("enter add!");
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

配置文件：

```
<bean id="commonDao" class="com.spring.dao.impl.CommonDaoImpl"></bean>
<bean id="commonService" class="com.spring.service.impl.CommonServiceImpl">
    <!-- 注入持久化访问所需的DAO组件 -->
    <property name="commonDao" ref="commonDao"/>
</bean>
```

以上是不使用注解的方式注入依赖对象（使用属性注入），下面来看一下使用注解为某个bean注入依赖对象。

（1）首先，在Spring容器的配置文件applicationContext.Xml文件中配置以下信息,该信心是一个Spring配置文件的模板.

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
<?xml version="1.0" encoding="UTF-8"?>
<beans
    xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context-3.0.xsd
    ">
    
</beans>
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

注意：只有配置了紫色部分的行才可以引入注解的命名空间，否则报错。以上的配置隐式的注册了多个对注释进行解析的处理器：AutowiredAnnotationBeanPostProcessor、CommonAnnotationBeanPostProcessor、PersistenceAnnotationBeanPostProcessor等。

（2）其次，在配置文件中打开<context:annotation-config>节点，告诉Spring容器可以用注解的方式注入依赖对象；其在配置文件中的代码如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
<beans>

……

<context:annotation-config></context:annotation-config>

……

</beans>
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

（3）第三，在配置文件中配置bean对象，如下：

```
<bean id="commonDao" class="com.spring.dao.impl.CommonDaoImpl"></bean>
<bean id="commonService" class="com.spring.service.impl.CommonServiceImpl"></bean>
```

（4）第四，在需要依赖注入的类中（本例中是CommonServiceImpl），声明一个依赖对象，不用生成该依赖对象的setter方法，并且为该对象添加注解。

 修改业务层实现类CommonServiceImpl

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
package com.spring.service.impl;

import javax.annotation.Resource;
import com.spring.dao.ICommonDao;
import com.spring.service.ICommonService;

public class CommonServiceImpl implements ICommonService{
    
    @Resource(name="commonDao")
    private ICommonDao commonDao;
    
//    // 依赖注入DAO组件所需的setter方法
//    public void setCommonDao(ICommonDao commonDao) {
//        this.commonDao = commonDao;
//    }
    
    public void add(){
        commonDao.add();
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

   其中，在Java代码中可以使用@Autowired或@Resource注解方式进行Spring的依赖注入。两者的区别是：@Autowired默认按类型装配，@Resource默认按名称装配，当找不到与名称匹配的bean时，才会按类型装配。

   比如：我们用@Autowired为上面的代码ICommonDao接口的实例对象进行注解，它会到Spring容器中去寻找与ICommonDao对象相匹配的类型，如果找到该类型则将该类型注入到commonDao字段中；

   如果用@Resource进行依赖注入，它先会根据指定的name属性去Spring容器中寻找与该名称匹配的类型，例如：@Resource(name="commonDao")，如果没有找到该名称，则会按照类型去寻找，找到之后，会对字段commonDao进行注入。

   使用注解注入依赖对象不用再在代码中写依赖对象的setter方法或者该类的构造方法，并且不用再配置文件中配置大量的依赖对象，使代码更加简洁，清晰，易于维护。

   在Spring IOC编程的实际开发中推荐使用注解的方式进行依赖注入。

**二、依赖注入——自动装配**

   在应用中，我们常常使用<ref>标签为JavaBean注入它依赖的对象,同时也Spring为我们提供了一个自动装配的机制，在定义Bean时，<bean>标签有一个autowire属性，我们可以通过指定它来让容器为受管JavaBean自动注入依赖对象。

自动装配是在配置文件中实现的，如下：<bean id="***" class="***" autowire="byType">

**只需要配置一个autowire属性即可完成自动装配，不用再配置文件中写,但是在类中还是要生成依赖对象的setter方法。**

<bean>的autowire属性有如下六个取值，他们的说明如下

1.No：即不启用自动装配。Autowire默认的值。默认情况下，需要通过"ref"来装配bean，如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
package com.lei.common;
 
public class Customer 
{
    private Person person;
 
    public Customer(Person person) {
        this.person = person;
    }
 
    public void setPerson(Person person) {
        this.person = person;
    }
    //...
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
package com.lei.common;
 
public class Person 
{
    //...
}
```

配置文件：

```
<bean id="customer" class="com.lei.common.Customer">
    <property name="person" ref="person" />
</bean>
<bean id="person" class="com.lei.common.Person" />
```

2.byName：按名称装配 可以根据属性的名称在容器中查询与该属性名称相同的bean，如果没有找到，则属性值为null。假设Boss类中有一个名为car的属性，如果容器中刚好有一个名为car的Bean，Spring就会自动将其装配给Boss的car属性。

   根据属性Property的名字装配bean，这种情况，Customer设置了autowire=*"byName"*，Spring会自动寻找与属性名字“person”相同的bean，找到后，通过调用setPerson(Person person)将其注入属性。

```
<bean id="customer" class="com.lei.common.Customer" autowire="byName" />
<bean id="person" class="com.lei.common.Person" />
```

如果根据 Property name找不到对应的bean配置，如下：

```
<bean id="customer" class="com.lei.common.Customer" autowire="byName" />
<bean id="person_another" class="com.lei.common.Person" />
```

Customer中Property名字是person，但是配置文件中找不到*person*，只有*person_another*，这时就会装配失败，运行后，Customer中person=null。

3.byType：按类型装配 可以根据属性类型，在容器中寻找该类型匹配的bean，如有多个，则会抛出异常，如果没有找到，则属性值为null。假设Boss类中有一个Car类型的属性，如果容器中刚好有一个Car类型的Bean，Spring就会自动将其装配给Boss的这个属性。

   根据属性Property的数据类型自动装配，这种情况，Customer设置了autowire=*"byType"*，Spring会总动寻找与属性类型相同的bean，找到后，通过调用setPerson(Person person)将其注入。

```
<bean id="customer" class="com.lei.common.Customer" autowire="byType" />
<bean id="person" class="com.lei.common.Person" />
```

 如果配置文件中有两个类型相同的bean会怎样呢？如下：

```
<bean id="customer" class="com.lei.common.Customer" autowire="byType" />
<bean id="person" class="com.lei.common.Person" />
<bean id="person_another" class="com.lei.common.Person" />
```

一旦配置如上，有两种相同数据类型的bean被配置，将抛出UnsatisfiedDependencyException异常，见以下：
Exception in thread "main" org.springframework.beans.factory.UnsatisfiedDependencyException:
所以，一旦选择了“byType”类型的自动装配，请确认你的配置文件中每个数据类型定义一个唯一的bean。

4.constructor：与byType方式相似，不同之处在与它应用于构造器参数，如果在容器中没有找到与构造器参数类型一致的bean，那么将抛出异常。（根据构造函数参数的数据类型，进行byType模式的自动装配。）

这种情况下，Spring会寻找与参数数据类型相同的bean，通过构造函数public Customer(Person person)将其注入。

```
<bean id="customer" class="com.lei.common.Customer" autowire="constructor" />
<bean id="person" class="com.lei.common.Person" />
```

5.autodetect：通过bean类的自省机制(introspection)来决定是使用constructor还是byType的方式进行自动装配。如果Bean有空构造器那么将采用“byType”自动装配方式，否则使用“constructor”自动装配方式。

这种情况下，Spring会先寻找Customer中是否有默认的构造函数，如果有相当于上边的"constructor"这种情况，用构造函数注入，否则，用"byType"这种方式注入，所以，此例中通过调用public Customer(Person person)将其注入。

```
<bean id="customer" class="com.lei.common.Customer" autowire="autodetect" />
<bean id="person" class="com.lei.common.Person" />
```

6.default：由上级标签<beans>的default-autowire属性确定。

注意：在配置bean时，<bean>标签中Autowire属性的优先级比其上级标签高，即是说，如果在上级标签中定义default-autowire属性为byName，而在<bean>中定义为byType时，Spring IoC容器会优先使用<bean>标签的配置。

小结：使用自动装配，配置文件简洁了许多。但是，自动装配并不是十全十美的，我们不论是使用byName还是byType的方法，Spring不一定就能很准确的为我们找到JavaBean依赖的对象。在这种情况下，你务必遵守javabean的命名规范，另外，如果使用自动装配，Spring配置文件的可读性也大大降低，我们不能很容易的看出个bean之间的依赖关系，这也在一定程度上降低了程序可维护性；也容易造成潜在的错误，比如说通过byName来装配，如果将属性 名字改了后，Spring就不会将其自动装配给Bean的属性了。

因此在使用自动装配时，应当权衡利弊，合理的与ref的方法相结合，尽量在降低工作量的同时，保证应用的可维护度。但是spring的reference还是不推荐在定义中用这个功能。

不是所有类型都能自动装配，不能自动装配的数据类型：Object、基本数据类型（Date、CharSequence、Number、URI、URL、Class、int）等。

**三、注入参数详解**

**·注入常量**

注入常量是依赖注入中最简单的。配置方式如下所示：

```
<property name="message" value="Hello World!"/>
或
<property name="index"><value>1</value></property>
```

以上两种方式都可以，从配置来看第一种更简洁。注意此处“value”中指定的全是字符串，由Spring容器将此字符串转换成属性所需要的类型，如果转换出错，将抛出相应的异常。

**·注入集合类型**

Java.util包中的集合类是最常用的数据结构类型，主要包括List、Set、Map、Properties，Spring为这些集合类型属性提供了专门的配置元素标签。

**1.List：需要使用标签来配置注入。**

新建一个Boss类，并在类中添加一个List类型的favorites属性。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
package com.spring.model;

import java.util.List;

public class Boss {
    
    private List favorites;

    public List getFavorites() {
        return favorites;
    }

    public void setFavorites(List favorites) {
        this.favorites = favorites;
    }
    
    public void print(){
        System.out.println(favorites);
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

对应Spring中的配置片段如下所示：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
<!-- 注入List类型属性 -->
<bean id="boss" class="com.spring.model.Boss">  
    <property name="favorites">
        <list>
            <value>看报</value>
            <value>赛车</value>
            <value>高尔夫</value>
        </list>
    </property>
</bean>
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

List属性既可以通过<value>注入字符串，也可以通过<ref>注入容器中的其他Bean。

**2.Set：需要使用标签来配置注入**，其配置参数及含义和<lsit>标签完全一样，具体配置例子如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
<bean id=”……” class=”……”>
    <property name="……">
        <set>  
            <value>value1</value>  
            <value>value2</value>  
            ……  
        </set>
    </property>
</bean> 
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

**3.Map：需要使用标签来配置注入，其属性“key-type”和“value-type”分别指定“键”和“值”的数据类型。**

在Boss类中添加一个Map类型的jobs属性。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
package com.spring.model;

import java.util.List;
import java.util.Map;

public class Boss {

    private Map jobs;
    
    public Map getJobs() {
        return jobs;
    }

    public void setJobs(Map jobs) {
        this.jobs = jobs;
    }

    public void print1(){
        System.out.println(jobs);
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

在配置文件中可以通过以下方式为jobs属性提供配置值。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
<!-- 注入Map类型属性 -->
<bean id="boss1" class="com.spring.model.Boss">  
    <property name="jobs">
        <map>
            <entry key="AM" value="会见客户" />
            <entry key="PM" value="公司内部会议" />
        </map>
    </property>
</bean> 
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

其中<map>表示Map注入，<entry>表示键值对，<key>表示键数据，<value>表示键所对应的值数据。

**4.Properties：需要使用标签来配置注入，键和值类型必须是String，不能变，子标签值来指定键值对。**

Properties类型其实可以看成是Map类型的特例。Map元素的键和值可以是任何类型的对象，而Properties属性的键和值都只能是字符串。为Boss添加一个Properties类型的mails属性。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
package com.spring.model;

import java.util.List;
import java.util.Map;
import java.util.Properties;

public class Boss {
    
    private Properties mails;
    
    public Properties getMails() {
        return mails;
    }

    public void setMails(Properties mails) {
        this.mails = mails;
    }
    
    public void print2(){
        System.out.println(mails);
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

下面的配置片段为mails提供了配置。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
<!-- 注入Properties类型属性 -->
<bean id="boss2" class="com.spring.model.Boss">  
    <property name="mails">
        <props>
            <prop key="jobMail">john-office@163.com</prop>
            <prop key="lifeMail">john-life@163.com</prop>
        </props>
    </property>
</bean>
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

因为Properties键值对只能是字符串，因此其配置比Map的配置要简单一些，注意值得配置没有<value>子元素标签。

**·引用其他Bean**

Spring IOC容器中定义的Bean可以相互引用，IOC容器则充当“红娘”的角色。下面在Boss类中添加一个Car类型的属性。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
package com.spring.model;

import java.util.List;
import java.util.Map;
import java.util.Properties;

public class Boss {
    
    private Car car;
    //设置car属性
    public void setCar(Car car) {
        this.car = car;
    }

    public void print3(){
        System.out.println(car.getBrand()+"----"+car.getPrice()+"----"+car.getMaxSpeed());
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

boss的Bean通过<ref>元素引用car Bean，建立起boss对car的依赖。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
<bean id="car" class="com.spring.model.Car">  
    <property name="maxSpeed" value="200"></property>
    <property name="brand" value="红旗CA72"></property>  
    <property name="price" value="200000.00"></property>
</bean>
<!-- 通过ref元素引用bean -->
<bean id="boss3" class="com.spring.model.Boss">  
    <property name="car">
        <!--引用上面定义的car Bean-->
        <ref bean="car"></ref>
    </property>
</bean>
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

<ref>元素可以通过以下3个属性引用容器中的其他Bean。
**·** bean：通过该属性可以引用同一容器或父容器的Bean，这是最常见的形式。

**·** local：通过该属性只能引用同一配置文件中定义的Bean，它可以利用XML解析器自动检验引用的合法性，以便在开发编写配置时能够及时发现并纠正配置的错误。

**·** parent:引用父容器中的Bean，如<ref parent="car">的配置说明car的Bean是父容器中的Bean。

为了说明子容器对父容器中Bean的引用，来看一个具体的例子。假设有两个配置文件beans1.xml和beans2.xml，其中beans1.xml被父容器加载，其配置内容如下。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
<?xml version="1.0" encoding="UTF-8"?>
<beans
    xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context-3.0.xsd
    ">
    
    <!--在父容器中定义的car -->
    <bean id="car" class="com.spring.model.Car">
        <property name="brand" value="红旗CA72"></property> 
        <property name="maxSpeed" value="200"></property> 
        <property name="price" value="200000.00"></property>
    </bean>
    
</beans>
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

而beans2.xml被子容器加载，其配置内容如下。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
<?xml version="1.0" encoding="UTF-8"?>
<beans
    xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:p="http://www.springframework.org/schema/p"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context-3.0.xsd
    ">
    
    <!--1.该Bean和父容器中的car Bean具有相同的id -->
    <bean id="car" class="com.spring.model.Car">  
        <property name="brand" value="吉利CT5"></property>  
        <property name="maxSpeed" value="100"></property>
        <property name="price" value="100000.00"></property>
    </bean>
    <bean id="boss" class="com.spring.model.Boss">  
        <property name="car">
            <!--引用父容器中的car，而非1处定义的Bean，如果采用<ref bean="car" />将引用本容器1处的car  -->
            <ref parent="car"></ref>
        </property>
    </bean>
</beans>
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

在beans1.xml中配置了一个car Bean，在beans2.xml中也配置了一个car Bean。分别通过父子容器加载beans1.xml和beans2.xml，beans2.xml中的boss通过<ref parent="car">将引用到父容器中的car。下面是分别加载beans1.xml和beans2.xml配置文件的代码。

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
/**
 * 引用父容器中的bean
 */
@Test
public void test9(){
    //父容器
    ApplicationContext pFactory=new ClassPathXmlApplicationContext("beans1.xml");
    //指定pFactory为该容器的父容器
    ApplicationContext factory=new ClassPathXmlApplicationContext(new String[]{"beans2.xml"},pFactory);
    Boss boss=(Boss) factory.getBean("boss");
    System.out.println("引用父容器中的bean");
    System.out.println(boss.getCar().toString());
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

运行这段代码，控制台中打印出以下的信息。

引用父容器中的bean
brand:红旗CA72/maxSpeed:200/price:200000.0