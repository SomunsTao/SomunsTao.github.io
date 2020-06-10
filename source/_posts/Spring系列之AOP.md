---
title: Spring系列之AOP
date: 2020-3-25
tags: Spring
categories: Spring
keywords: spring
description: Spring
---



![img](http://java.tedu.cn/upload/20180306/20180306104159_146.jpg)

原文链接：https://www.cnblogs.com/xiaoxi/p/5945707.html

原作者：平凡希

------

------

# [Spring系列之AOP](https://www.cnblogs.com/xiaoxi/p/5945707.html)

**一、什么是AOP**
AOP（Aspect-OrientedProgramming，面向方面编程），可以说是OOP（Object-Oriented Programing，面向对象编程）的补充和完善。OOP引入封装、继承和多态性等概念来建立一种对象层次结构，用以模拟公共行为的一个集合。当我们需要为分散的对象引入公共行为的时候，OOP则显得无能为力。也就是说，OOP允许你定义从上到下的关系，但并不适合定义从左到右的关系。例如日志功能。日志代码往往水平地散布在所有对象层次中，而与它所散布到的对象的核心功能毫无关系。对于其他类型的代码，如安全性、异常处理和透明的持续性也是如此。这种散布在各处的无关的代码被称为横切（cross-cutting）代码，在OOP设计中，它导致了大量代码的重复，而不利于各个模块的重用。

而AOP技术则恰恰相反，它利用一种称为“横切”的技术，剖解开封装的对象内部，并将那些影响了多个类的公共行为封装到一个可重用模块，并将其名为"Aspect"，即方面。所谓“方面”，简单地说，就是将那些与业务无关，却为业务模块所共同调用的逻辑或责任封装起来，便于减少系统的重复代码，降低模块间的耦合度，并有利于未来的可操作性和可维护性。AOP代表的是一个横向的关系，如果说“对象”是一个空心的圆柱体，其中封装的是对象的属性和行为；那么面向方面编程的方法，就仿佛一把利刃，将这些空心圆柱体剖开，以获得其内部的消息。而剖开的切面，也就是所谓的“方面”了。然后它又以巧夺天功的妙手将这些剖开的切面复原，不留痕迹。

使用“横切”技术，AOP把软件系统分为两个部分：核心关注点和横切关注点。业务处理的主要流程是核心关注点，与之关系不大的部分是横切关注点。横切关注点的一个特点是，他们经常发生在核心关注点的多处，而各处都基本相似。比如权限认证、日志、事务处理。Aop 的作用在于分离系统中的各种关注点，将核心关注点和横切关注点分离开来。正如Avanade公司的高级方案构架师Adam Magee所说，AOP的核心思想就是“将应用程序中的商业逻辑同对其提供支持的通用服务进行分离。”

实现AOP的技术，主要分为两大类：一是采用动态代理技术，利用截取消息的方式，对该消息进行装饰，以取代原有对象行为的执行；二是采用静态织入的方式，引入特定的语法创建“方面”，从而使得编译器可以在编译期间织入有关“方面”的代码。

**二、AOP使用场景**
AOP用来封装横切关注点，具体可以在下面的场景中使用:
Authentication 权限
Caching 缓存
Context passing 内容传递
Error handling 错误处理
Lazy loading　懒加载
Debugging　　调试
logging, tracing, profiling and monitoring　记录跟踪　优化　校准
Performance optimization　性能优化
Persistence　　持久化
Resource pooling　资源池
Synchronization　同步
Transactions 事务

**三、AOP相关概念**

**1.连接点（Joinpoint）**
   程序执行的某个特定位置：如类开始初始化之前、类初始化之后、类某个方法调用前、调用后等；一个类或一段程序代码拥有一些具有边界性质的特定点，这些代码中的特定点就成为“连接点”，Spring仅支持方法的连接点，即仅能在方法调用前、方法调用后以及方法调用前后的这些程序执行点织入增强。比如：黑客攻击系统需要找到突破口，没有突破口就没有办法攻击，从某种程度上来说，AOP就是一个黑客，连接点就是AOP向目标类攻击的候选点。

   连接点有两个信息确定：第一是用方法表示的程序执行点；第二是用相对点表示的方位；如在Test.foo()方法执行前的连接点，执行点为Test.foo，方位为该方法执行前的位置。Spring使用切点对执行点进行定位，而方位则在增强类型中定义。

**2.切点（Pointcut）**
   每个程序类都拥有许多连接点，如一个拥有两个方法的类，这两个方法都是连接点，即连接点是程序类中客观存在的事物。但在为数众多的连接点中，如何定位到某个连接点上呢？AOP通过切点定位特定连接点。通过数据库查询的概念来理解切点和连接点：连接点相当于数据库表中的记录，而切点相当于查询条件。连接点和切点不是一一对应的关系，一个切点可以匹配多个连接点。

   在Spring中，切点通过org.springframework.aop.Pointcut接口进行描述，它使用类和方法作为连接点的查询条件，Spring AOP的规则解析引擎负责解析切点所设定的查询条件，找到对应的连接点；其实确切的说应该是执行点而非连接点，因为连接点是方法执行前、执行后等包括方位信息的具体程序执行点，而切点只定位到某个方法上，所以如果希望定位到具体连接点上，还需要提供方位信息。

**3.增强（Advice）**
   增强是织入到目标类连接点上的一段程序代码（好比AOP以黑客的身份往业务类中装入木马），增强还拥有一个和连接点相关的信息，这便是执行点的方位。结合执行点方位信息和切点信息，我们就可以找到特定的连接点了，所以Spring提供的增强接口都是带方位名的：BefortAdvice、AfterReturningAdvice、ThrowsAdvice等。（有些将Advice翻译为通知，但通知就是把某个消息传达给被通知者，并没有为被通知者做任何事情，而Spring的Advice必须嵌入到某个类的连接点上，并完成了一段附加的应用逻辑；）

**4.目标对象（Target）**
   增强逻辑的织入目标类，如果没有AOP，目标业务类需要自己实现所有逻辑，在AOP的帮助下，目标类只实现那些非横切逻辑的程序逻辑，而其他监测代码则可以使用AOP动态织入到特定的连接点上。

**5.引介（Introduction）**
   引介是一种特殊的增强，它为类添加一些属性和方法，这样即使一个业务类原本没有实现某个接口，通过AOP的引介功能，我们可以动态的为该业务类添加接口的实现逻辑，让这个业务类成为这个接口的实现类。

**6.织入（Weaving）**
织入是将增强添加到目标类具体连接点上的过程，AOP就像一台织布机，将目标类、增强或者引介编织到一起，AOP有三种织入的方式：
a.编译期间织入，这要求使用特殊的java编译器；
b.类装载期织入，这要求使用特殊的类装载器；
c.动态代理织入，在运行期为目标类添加增强生成子类的方式。
Spring采用动态代理织入，而AspectJ采用编译器织入和类装载期织入。

**7.代理（Proxy）**
一个类被AOP织入增强后，就产生出了一个结果类，它是融合了原类和增强逻辑的代理类。

**8.切面（Aspect）**
切面由切点和增强组成，它既包括了横切逻辑的定义，也包括了连接点的定义，Spring AOP就是负责实施切面的框架，它将切面所定义的横切逻辑织入到切面所指定的连接点中。

**总结：AOP的工作重点就是如何将增强应用于目标对象的连接点上，这里首先包括两个工作：第一，如何通过切点和增强定位到连接点；第二，如何在增强中编写切面的代码。**

**四、实现原理**

**1.代理模式**

**代理的概念：简单的理解就是通过为某一个对象创建一个代理对象，我们不直接引用原本的对象，而是由创建的代理对象来控制对原对象的引用。**

代理模式是常用的java设计模式，他的特征是代理类与委托类(或目标类)有同样的接口，代理类主要负责为委托类预处理消息、过滤消息、把消息转发给委托类，以及事后处理消息等。代理类与委托类之间通常会存在关联关系，一个代理类的对象与一个委托类的对象关联，代理类的对象本身并不真正实现服务，而是通过调用委托类的对象的相关方法，来提供特定的服务。
按照代理的创建时期，代理类可以分为两种。
**静态代理：**由程序员创建或特定工具自动生成源代码，再对其编译。在程序运行前，代理类的.class文件就已经存在了。
**动态代理：**在程序运行时，运用反射机制动态创建而成，无需手动编写代码。动态代理不仅简化了编程工作，而且提高了软件系统的可扩展性，因为Java反射机制可以生成任意类型的动态代理类。

**代理原理：**代理对象内部含有对真实对象的引用，从而可以操作真实对象，同时代理对象提供与真实对象相同的接口以便在任何时刻都能代替真实对象。同时，代理对象可以在执行真实对象操作时，附加其他的操作，相当于对真实对象进行封装。

**2.AOP的实现原理**

**AOP的实现关键在于AOP框架自动创建的AOP代理。AOP代理主要分为两大类：
.静态代理：使用AOP框架提供的命令进行编译，从而在编译阶段就可以生成AOP代理类，因此也称为编译时增强；静态代理一Aspectj为代表。
.动态代理：在运行时借助于JDK动态代理，CGLIB等在内存中临时生成AOP动态代理类，因此也被称为运行时增强，Spring AOP用的就是动态代理。**

AOP分为静态AOP和动态AOP。静态AOP是指AspectJ实现的AOP，他是将切面代码直接编译到Java类文件中。动态AOP是指将切面代码进行动态织入实现的AOP。Spring的AOP为动态AOP，实现的技术为：**JDK提供的动态代理技术** 和 **CGLIB(动态字节码增强技术)**。**尽管实现技术不一样，但都是基于代理模式，都是生成一个代理对象。**

**（1）JDK动态代理**

**a. JDK动态代理是面向接口的，必须提供一个委托类和代理类都要实现的接口，只有接口中的方法才能够被代理。
b. JDK动态代理的实现主要使用java.lang.reflect包里的Proxy类和InvocationHandler接口。**

**InvocationHandler接口：**

来看看java的API帮助文档是怎么样描述InvocationHandler接口的：

```
InvocationHandler is the interface implemented by the invocation handler of a proxy instance. 

Each proxy instance has an associated invocation handler. When a method is invoked on a proxy instance, the method invocation is encoded and dispatched to the invoke method of its invocation handler.
```

说明：每一个动态代理类都必须要实现InvocationHandler这个接口，并且每个代理类的实例都关联到了一个handler，当我们通过代理对象调用一个方法的时候，这个方法的调用就会被转发为由InvocationHandler这个接口的 invoke 方法来进行调用。同时在invoke的方法里 我们可以对被代理对象的方法调用做增强处理(如添加事务、日志、权限验证等操作)。我们来看看InvocationHandler这个接口的唯一一个方法 invoke 方法：

```
public interface InvocationHandler { 
     public Object invoke(Object proxy,Method method,Object[] args) throws Throwable; 
}
```

参数说明：
Object proxy：指被代理的对象。
Method method：要调用的方法。(指代的是我们所要调用代理对象的某个方法的Method对象)
Object[] args：方法调用时所需要的参数。(指代的是调用真实对象某个方法时接受的参数)

可以将InvocationHandler接口的子类想象成一个代理的最终操作类，替换掉ProxySubject。 

**Proxy类：**
Proxy类是专门完成代理的操作类，可以通过此类为一个或多个接口动态地生成实现类，此类提供了如下的操作方法：

```
public static Object newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler h) throws IllegalArgumentException 
```

参数说明：
ClassLoader loader：类加载器
Class<?>[] interfaces：得到全部的接口
InvocationHandler h：得到InvocationHandler接口的子类实例

**JDK动态代理示例：**

定义一个业务接口IUserService，如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
package com.spring.aop;

public interface IUserService {
    //添加用户
    public void addUser();
    //删除用户
    public void deleteUser();
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

一个简单的实现类UserServiceImpl，如下：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
package com.spring.aop;

public class UserServiceImpl implements IUserService{
    
    public void addUser(){
        System.out.println("新增了一个用户！");
    }
    
    public void deleteUser(){
        System.out.println("删除了一个用户！");
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

现在我们要实现的是，在addUser和deleteUser之前和之后分别动态植入处理。
JDK动态代理主要用到java.lang.reflect包中的两个类：Proxy和InvocationHandler。
InvocationHandler是一个接口，通过实现该接口定义横切逻辑，并通过反射机制调用目标类的代码，动态的将横切逻辑和业务逻辑编织在一起。
Proxy利用InvocationHandler动态创建一个符合某一接口的实例，生成目标类的代理对象。
如下，我们创建一个InvocationHandler实例DynamicProxy：**(当执行动态代理对象里的目标方法时，实际上会替换成调用DynamicProxy的invoke方法)**

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
package com.spring.aop;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;

public class DynamicProxy implements InvocationHandler{
    
    //被代理对象（就是要给这个目标类创建代理对象）
    private Object target;
    
    //传递代理目标的实例，因为代理处理器需要，也可以用set等方法。
    public DynamicProxy(Object target){
        this.target=target;
    }
    
    /**
     * 覆盖java.lang.reflect.InvocationHandler的方法invoke()进行织入(增强)的操作。
     * 这个方法是给代理对象调用的，留心的是内部的method调用的对象是目标对象，可别写错。
     * 参数说明：
     * proxy是生成的代理对象，method是代理的方法，args是方法接收的参数
     */
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable{
        //目标方法之前执行
        System.out.println("do sth Before...");
        //通过反射机制来调用目标类方法
        Object result = method.invoke(target, args);
        //目标方法之后执行
        System.out.println("do sth After...\n");
        return result;
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

 下面是测试：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
package com.spring.aop;

//用java.lang.reflect.Proxy.newProxyInstance()方法创建动态实例来调用代理实例的方法
import java.lang.reflect.Proxy;

public class DynamicTest {
    
    public static void main(String[] args){
        //希望被代理的目标业务类
        IUserService target = new UserServiceImpl();
        //将目标类和横切类编织在一起
        DynamicProxy handler= new DynamicProxy(target);
        //创建代理实例，它可以看作是要代理的目标业务类的加多了横切代码(方法)的一个子类
        //创建代理实例(使用Proxy类和自定义的调用处理逻辑(handler)来生成一个代理对象)
        IUserService proxy = (IUserService)Proxy.newProxyInstance(
                target.getClass().getClassLoader(),//目标类的类加载器
                target.getClass().getInterfaces(), //目标类的接口
                handler); //横切类
        proxy.addUser();
        proxy.deleteUser();
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

说明：上面的代码完成业务类代码和横切代码的编制工作，并生成了代理实例，newProxyInstance方法的第一个参数为类加载器，第二个参数为目标类所实现的一组接口，第三个参数是整合了业务逻辑和横切逻辑的编织器对象。

每一个动态代理实例的调用都要通过InvocationHandler接口的handler（调用处理器）来调用，动态代理不做任何执行操作，只是在创建动态代理时，把要实现的接口和handler关联，动态代理要帮助被代理执行的任务，要转交给handler来执行。其实就是调用invoke方法。(可以看到执行代理实例的addUser()和deleteUser()方法时执行的是DynamicProxy的invoke()方法。)

运行结果：
![img](https://images2015.cnblogs.com/blog/249993/201610/249993-20161012141324750-1830711632.png)

基本流程：用Proxy类创建目标类的动态代理，创建时需要指定一个自己实现InvocationHandler接口的回调类的对象，这个回调类中有一个invoke()用于拦截对目标类各个方法的调用。创建好代理后就可以直接在代理上调用目标对象的各个方法。

实现动态代理步骤：
A. 创建一个实现接口InvocationHandler的类，他必须实现invoke方法。
B．创建被代理的类以及接口。
C．通过Proxy的静态方法newProxyInstance（ClassLoader loader, Class<?>[]interfaces, InvocationHandler handler）创建一个代理。
D．通过代理调用方法。

**使用JDK动态代理有一个很大的限制，就是它要求目标类必须实现了对应方法的接口，它只能为接口创建代理实例。**我们在上文测试类中的Proxy的newProxyInstance方法中可以看到，该方法第二个参数便是目标类的接口。如果该类没有实现接口，这就要靠cglib动态代理了。

**（2）CGLIB动态代理**

CGLib采用非常底层的字节码技术，可以为一个类创建一个子类，并在子类中采用方法拦截的技术拦截所有父类方法的调用，并顺势植入横切逻辑。

字节码生成技术实现AOP，其实就是继承被代理对象，然后Override需要被代理的方法，在覆盖该方法时，自然是可以插入我们自己的代码的。因为需要Override被代理对象的方法，所以自然用CGLIB技术实现AOP时，就必须要求需要被代理的方法不能是final方法，因为final方法不能被子类覆盖。

a.使用CGLIB动态代理不要求必须有接口，生成的代理对象是目标对象的子类对象，**所以需要代理的方法不能是private或者final或者static的。**
b.使用CGLIB动态代理需要有对cglib的jar包依赖（导入asm.jar和cglib-nodep-2.1_3.jar）

CGLibProxy与JDKProxy的代理机制基本类似，只是其动态代理的代理对象并非某个接口的实现，而是针对目标类扩展的子类。换句话说JDKProxy返回动态代理类，是目标类所实现接口的另一个实现版本，它实现了对目标类的代理（如同UserDAOProxy与UserDAOImp的关系），而CGLibProxy返回的动态代理类，则是目标代理类的一个子类（代理类扩展了UserDaoImpl类）

**cglib 代理特点：**
CGLIB 是针对类来实现代理，它的原理是对指定的目标类生成一个子类，并覆盖其中方法。因为采用的是继承，**所以不能对 finall 类进行继承**。

我们使用CGLIB实现上面的例子：

代理的最终操作类：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
package com.spring.aop;

import java.lang.reflect.Method;

import net.sf.cglib.proxy.Enhancer;
import net.sf.cglib.proxy.MethodInterceptor;
import net.sf.cglib.proxy.MethodProxy;

public class CglibProxy implements MethodInterceptor{
    
    //增强器，动态代码生成器
    Enhancer enhancer = new Enhancer();
    
    /**
     * 创建代理对象
     * @param clazz
     * @return 返回代理对象
     */
    public Object getProxy(Class clazz){
        //设置父类，也就是被代理的类(目标类)
        enhancer.setSuperclass(clazz);
        //设置回调（在调用父类方法时，回调this.intercept()）
        enhancer.setCallback(this);
        //通过字节码技术动态创建子类实例(动态扩展了UserServiceImpl类)
        return enhancer.create();
    }
    
    /**
     * 拦截方法：在代理实例上拦截并处理目标方法的调用，返回结果
     * obj:目标对象代理的实例;
     * method:目标对象调用父类方法的method实例;
     * args:调用父类方法传递参数;
     * proxy:代理的方法去调用目标方法
     */
    public Object intercept(Object obj,Method method,Object[] args,MethodProxy proxy) 
        throws Throwable{
        
        System.out.println("--------测试intercept方法的四个参数的含义-----------");
        System.out.println("obj:"+obj.getClass());
        System.out.println("method:"+method.getName());
        System.out.println("proxy:"+proxy.getSuperName());
        if(args!=null&&args.length>0){
            for(Object value : args){
                System.out.println("args:"+value);
            }
        }

        //目标方法之前执行
        System.out.println("do sth Before...");
        //目标方法调用
        //通过代理类实例调用父类的方法，即是目标业务类方法的调用
        Object result = proxy.invokeSuper(obj, args);
        //目标方法之后执行
        System.out.println("do sth After...\n");
        return result;
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

测试类：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
package com.spring.aop;

public class CglibProxyTest {
    
    public static void main(String[] args){
        CglibProxy proxy=new CglibProxy();
        //通过java.lang.reflect.Proxy的getProxy()动态生成目标业务类的子类，即是代理类，再由此得到代理实例
        //通过动态生成子类的方式创建代理类
        IUserService target=(IUserService)proxy.getProxy(UserServiceImpl.class);
        target.addUser();
        target.deleteUser();
    }
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

基本流程：需要自己写代理类，它实现MethodInterceptor接口，有一个intercept()回调方法用于拦截对目标方法的调用，里面使用methodProxy来调用目标方法。创建代理对象要用Enhance类，用它设置好代理的目标类、由intercept()回调的代理类实例、最后用create()创建并返回代理实例。

输出：

![img](https://images2015.cnblogs.com/blog/249993/201610/249993-20161013160645015-1062752320.png)

我们看到达到了同样的效果。它的原理是生成一个父类enhancer.setSuperclass(clazz)的子类enhancer.create()，然后对父类的方法进行拦截enhancer.setCallback(this). 对父类的方法进行覆盖，所以父类方法不能是final的。

**总结：**
　　(1).通过输出可以看出，最终调用的是com.spring.aop.UserServiceImpl的子类(也是代理类)com.spring.aop.UserServiceImpl$$EnhancerByCGLIB$$43831205的方法。
　　(2). private,final和static修饰的方法不能被代理。

**注意：**
　　(1).CGLIB是通过实现目标类的子类来实现代理，不需要定义接口。
　　(2).生成代理对象使用最多的是通过Enhancer和继承了Callback接口的MethodInterceptor接口来生成代理对象，设置callback对象的作用是当调用代理对象方法的时候会交给callback对象的来处理。
　　(3).创建子类对象是通过使用Enhancer类的对象，通过设置enhancer.setSuperClass(Class class)和enhancer.setCallback(Callback callback)来创建代理对象。

解释MethodInterceptor接口的intercept方法：

```
Object intercept(Object var1, Method var2, Object[] var3, MethodProxy var4) throws Throwable;
```

参数说明：Object var1代表的是子类代理对象，Method var2代表的是要调用的方法反射对象，第三个参数是传递给调用方法的参数，前三个参数和JDK的InvocationHandler接口的invoke方法中参数含义是一样的，第四个参数MethodProxy对象是cglib生成的用来代替method对象的，使用此对象会比jdk的method对象的效率要高。

如果使用method对象来调用目标对象的方法: method.invoke(var1, var3)，则会陷入无限递归循环中， 因为此时的目标对象是目标类的子代理类对象。

MethodProxy类提供了两个invoke方法：

```
public Object invokeSuper(Object obj, Object[] args) throws Throwable;
public Object invoke(Object obj, Object[] args) throws Throwable;
```

注意此时应该使用invokeSuper()方法，顾名思义调用的是父类的方法，若使用invoke方法，则需要提供一个目标类对象，但我们只有目标类子类代理对象，所以会陷入无限递归循环中。

CGLIB所创建的动态代理对象的性能比JDK所创建的动态代理对象的性能高很多，但创建动态代理对象时比JDK创建动态代理对象要花费更长的时间。

**JDK代理和CGLIB代理的总结(生成代理对象的前提是有AOP切入)
**(1)、如果目标对象实现了接口，默认情况下会采用JDK的动态代理实现AOP。 如果就是单纯的用IOC生成一个对象，也没有AOP的切入不会生成代理的，只会NEW一个实例，给Spring的Bean工厂。
(2)、如果目标对象实现了接口，可以强制使用CGLIB实现AOP
如何强制使用CGLIB实现AOP
\* 添加CGLIB库
\* 在spring配置文件中加入<aop:aspectj-autoproxy proxy-target-class="true"/>就能强制使用
(3)、如果目标对象没有实现了接口，必须采用CGLIB库，spring会自动在JDK动态代理和CGLIB之间转换（没有实现接口的就用CGLIB代理，使用了接口的类就用JDK动态代理）

**JDK动态代理和CGLIB字节码生成的区别：**
(1)、**JDK动态代理只能对实现了接口的类生成代理，而不能针对类。CGLIB是针对类实现代理，主要是对指定的类生成一个子类，覆盖其中的方法。因为是继承，所以该类或方法最好不要声明成final。**
(2)、JDK代理是不需要依赖第三方的库，只要JDK环境就可以进行代理，它有几个要求
\* 实现InvocationHandler;
\* 使用Proxy.newProxyInstance产生代理对象;
\* 被代理的对象必须要实现接口;
CGLib 必须依赖于CGLib的类库，但是它需要类来实现任何接口代理的是指定的类生成一个子类，覆盖其中的方法，是一种继承。
(3)、jdk的核心是实现InvocationHandler接口，使用invoke()方法进行面向切面的处理，调用相应的通知。cglib的核心是实现MethodInterceptor接口，使用intercept()方法进行面向切面的处理，调用相应的通知。

**五、小结**

   AOP 广泛应用于处理一些具有横切性质的系统级服务，AOP 的出现是对 OOP 的良好补充，它使得开发者能用更优雅的方式处理具有横切性质的服务。不管是哪种 AOP 实现，不论是 AspectJ、还是 Spring AOP，它们都需要动态地生成一个 AOP 代理类，区别只是生成 AOP 代理类的时机不同：AspectJ 采用编译时生成 AOP 代理类，因此具有更好的性能，但需要使用特定的编译器进行处理；而 Spring AOP 则采用运行时生成 AOP 代理类，因此无需使用特定编译器进行处理。由于 Spring AOP 需要在每次运行时生成 AOP 代理，因此性能略差一些。