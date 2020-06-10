---
title: Spring：源码解析
date: 2020-3-30
tags: Spring
categories: Spring
keywords: Spring
description: Spring
---

# 	**Spring：源码解析**

优秀的源码中有着多年沉积下来的精华，这些精华是非常值得我们学习的。放弃阅读源码，你将失去一个和大师学习的机会。

用Spring框架做了几年的开发，只停留在会用的阶段上，然而Spring的设计思想和原理确实一个巨大的宝库。大部分人仅仅知道怎么去配，或着加上什么属性就能达到什么效果，这些东西都可以通过查文档，查google来解决。关键是在怎么理解它，把它的思想变为自己的东西。这几天没有学习(装逼)，感觉心里甚是空虚，索性研究了下一直很好奇的Spring大佬,毕竟写代码天天都在用。

前方高能，非战斗人员迅速投入战斗:

1. Spring IOC容器
2. ApplicationContext与BeanFactory探究.
3. bean的加载
4. FactoryBean
5. Spring AOP实现原理及实战

文章篇幅有限，只对Spring几个重要的知识点进行简单阐述，有兴趣的可以看看《Spring源码深度解析》，该书下载地址：https://pan.baidu.com/s/1jGxdGTg，本文也是基于该书总结出来的。

1.Spring IOC容器

很多人一提IOC，便张口就来：控制反转。究竟哪些方面被反转了呢？答案是依赖对象的获得被反转了。很多时候，我们通过多个对象之间的协作来完成一个功能，如果获取所依赖对象靠自身来实现，这将导致代码的耦合度高和难以测试。当然,控制反转还有一个好听的名字:依赖注入。

Spring IOC通过引入xml配置，由IOC容器来管理对象的生命周期,依赖关系等。

![img](http://5b0988e595225.cdn.sohucs.com/images/20180508/c79a9018721948f3a6e6eeb358ac8ee3.png)

从图中可以看出，我们以前获取两个有依赖关系的对象，要用set方法，而用容器之后，它们之间的关系就由容器来管理。那么，Spring容器的加载过程是什么样的呢?

![img](http://5b0988e595225.cdn.sohucs.com/images/20180508/dfa81c829b234c5584ffd09899dd1250.png)

BeanDefinition是一个接口，用于属性承载，比如<bean>元素标签拥有class、scope、lazy-init等配置。bean的定义方式有千千万万种，无论是何种标签，无论是何种资源定义，无论是何种容器，只要按照Spring的规范编写xml配置文件，最终的bean定义内部表示都将转换为内部的唯一结构：BeanDefinition。当BeanDefinition注册完毕以后，Spring的BeanFactory就可以随时根据需要进行实例化了。

2.ApplicationContext与BeanFactory探究

实例化的工作会在容器启动后过AbstractApplicationContext中reflash方法自动进行。我们常用的ApplicationContext实现类ClassPathXmlApplicationContext继承了AbstractApplicationContext类，继承关系如下图.

![img](http://5b0988e595225.cdn.sohucs.com/images/20180508/345d95a6fce6447d8823bac6ae14f7c0.png)

AbstractApplicationContext里的reflash方法是Spring初始IOC容器一个非常重要的方法，不管你是ApplicationContext哪个实现类，最终都会进入这个方法。

@Override

publicvoidrefresh()throwsBeansException, IllegalStateException{

synchronized( this.startupShutdownMonitor) {

// 设置和校验系统变量和环境变量的值

prepareRefresh();

//主要是创建beanFactory，同时加载配置文件.xml中的beanDefinition

//通过String[] configLocations = getConfigLocations()获取资源路径，然后加载beanDefinition

ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();

//给beanFactory注册一些标准组建，如ClassLoader，StandardEnvironment，BeanProcess

prepareBeanFactory(beanFactory);

try{

//提供给子类实现一些postProcess的注册，如AbstractRefreshableWebApplicationContext注册一些Servlet相关的

//postProcess，真对web进行生命周期管理的Scope，通过registerResolvableDependency()方法注册指定ServletRequest，HttpSession，WebRequest对象的工厂方法

postProcessBeanFactory(beanFactory);

//调用所有BeanFactoryProcessor的postProcessBeanFactory()方法

invokeBeanFactoryPostProcessors(beanFactory);

//注册BeanPostProcessor，BeanPostProcessor作用是用于拦截Bean的创建

registerBeanPostProcessors(beanFactory);

//初始化消息Bean

initMessageSource();

//初始化上下文的事件多播组建，ApplicationEvent触发时由multicaster通知给ApplicationListener

initApplicationEventMulticaster();

//ApplicationContext初始化一些特殊的bean

onRefresh();

//注册事件监听器，事件监听Bean统一注册到multicaster里头，ApplicationEvent事件触发后会由multicaster广播

registerListeners();

//非延迟加载的单例Bean实例化

finishBeanFactoryInitialization(beanFactory);

finishRefresh();

}

catch(BeansException ex) {

logger.warn( "Exception encountered during context initialization - cancelling refresh attempt", ex);

destroyBeans();

cancelRefresh(ex);

throwex;

}

}

}

代码逻辑清晰的值得mark一下。这个方法的作用是创建加载Spring容器配置（包括.xml配置，property文件和数据库模式等）。

BeanFactory体系结构是典型的工厂方法模式，即什么样的工厂生产什么样的产品。要知道工厂是如何产生对象的，我们需要看具体的IOC容器实现，具体的实现有：如 DefaultListableBeanFactory 、 XmlBeanFactory 、 ApplicationContext 等。那么，究竟BeanFactory里到底是什么样的呢？

packageorg.springframework.beans.factory;

publicinterfaceBeanFactory{

/**

\* 用来引用一个实例，或把它和工厂产生的Bean区分开，就是说，如果一个FactoryBean的名字为a，那么，&a会得到那个Factory

*/

String FACTORY_BEAN_PREFIX = "&";

/*

\* 四个不同形式的getBean方法，获取实例

*/

ObjectgetBean(String name)throwsBeansException;

<T> TgetBean(String name, Class<T> requiredType)throwsBeansException;

<T> TgetBean(Class<T> requiredType)throwsBeansException;

ObjectgetBean(String name, Object... args)throwsBeansException;

booleancontainsBean(String name); // 是否存在

booleanisSingleton(String name)throwsNoSuchBeanDefinitionException; // 是否为单实例

booleanisPrototype(String name)throwsNoSuchBeanDefinitionException; // 是否为原型（多实例）

booleanisTypeMatch(String name, Class<?> targetType)

throwsNoSuchBeanDefinitionException; // 名称、类型是否匹配

Class<?> getType(String name) throwsNoSuchBeanDefinitionException; // 获取类型

String[] getAliases(String name); // 根据实例的名字获取实例的别名

}

我们可以看出BeanFactory里只对IOC容器的基本行为作了定义，根本不关心你的bean是如何定义怎样加载的，它规定了所有的容器至少需要实现的标准。说到实现，BeanFactory有几个比较重要的实现类需要知道，ref：【Spring4揭秘 BeanFactory】基本容器-BeanFactory：https://blog.csdn.net/u011179993/article/details/51636742。那么BeanFactory的基本实现类XmlBeanFactory与我们常用的ApplicationContext有什么区别呢?答案是bean的加载。

3.bean的加载

我们先看一道面试经常会问到的问题:**Spring的bean在什么时候实例化?**——第一：如果你使用BeanFactory，如XmlBeanFactory作为Spring Bean的工厂类，则所有的bean都是在第一次使用该bean的时候实例化 。第二：如果你使用ApplicationContext作为Spring Bean的工厂类，则又分为以下几种情况：

1. 如果bean的scope是singleton的，并且lazy-init为false（默认是false，所以可以不用设置），则ApplicationContext启动的时候就实例化该bean，并且将实例化的bean放在一个线程安全的 ConcurrentHashMap 结构的缓存中，下次再使用该Bean的时候，直接从这个缓存中取 。
2. 如果bean的scope是singleton的，并且lazy-init为true，则该bean的实例化是在第一次使用该bean的时候进行实例化 。
3. 如果bean的scope是prototype的，则该bean的实例化是在第一次使用该Bean的时候进行实例化 。

ClassPathXmlApplicationContext有几个重载的构造函数最终都会调用父类AbstractApplicationContext的reflash方法，reflash方法在前文有介绍，作用是创建加载Spring容器配置。AbstractApplicationContext也有getBean方法：

AbstractApplicationContext下的代码：

publicObjectgetBean( Stringname) throws BeansException {

//Bean的获取外部容器交给了内部容器

returngetBeanFactory().getBean(name);

}

内部容器由DefaultListableBeanFactory承当，但真实的getBean方法实现是由其父类AbstractBeanFactory实现的，AbstractBeanFactory类同样实现了BeanFactory接口的方法，它有四个重载的getBean方法，不管哪一个都会去调用doGetBean方法：

![img](http://5b0988e595225.cdn.sohucs.com/images/20180508/fb9021a0e35f4c639606d43903c4a182.jpeg)

![img](http://5b0988e595225.cdn.sohucs.com/images/20180508/cb5912600cee482ba9767ebdd66e721d.png)

那么doGetBean里干了什么事情呢？

protected<T> T doGetBean(

final Stringname, final Class<T> requiredType, final Object[] args, booleantypeCheckOnly)

throws BeansException {

//bean name处理，去除FactoryBean前缀等

final StringbeanName = transformedBeanName(name);

Objectbean = null;

//先从singleton缓存中查看是否已经实例化过该Bean，根据是否有缓存分为两个分支分别处理

ObjectsharedInstance = getSingleton(beanName);

if(sharedInstance != null&& args == null) {

// 分支一，若缓存中获取到了并且该BeanDefinition信息表明该bean是singleton的，直接将获取到的缓存Bean

//(有可能是半成品)交给getObjectForBeanInstance处理

/*.........省略logger部分代码............*/

//调用getObjectForBeanInstance处理

bean = getObjectForBeanInstance(sharedInstance, name, beanName, null);

} else{

// 分之二：没有缓存，则需要从头实例化该bean

// We're assumably within a circular reference.

if(isPrototypeCurrentlyInCreation(beanName)) {

thrownewBeanCurrentlyInCreationException(beanName);}

// 检查BeanDefinition是否在当前工厂或父工厂

BeanFactory parentBeanFactory = getParentBeanFactory();

if(parentBeanFactory != null&& !containsBeanDefinition(beanName)) {

// Not found -> check parent.

StringnameToLookup = originalBeanName(name);

if(args != null) {

// 父工厂getBean

returnparentBeanFactory.getBean(nameToLookup, args);

}

else{

// No args -> delegate to standard getBean method.

returnparentBeanFactory.getBean(nameToLookup, requiredType);

}

}

//将bean加入“正在创建”的集合，完成后会remove,对应afterSingletonCreation/afterPrototypeCreation方法

if(!typeCheckOnly) {

markBeanAsCreated(beanName);

}

final RootBeanDefinition mbd = getMergedLocalBeanDefinition(beanName);

checkMergedBeanDefinition(mbd, beanName, args);

// 解决依赖关系，将依赖的bean提前实例化

String[] dependsOn = mbd.getDependsOn();

if(dependsOn != null) {

for(int i = 0; i < dependsOn.length; i++) {

StringdependsOnBean = dependsOn[i];

getBean(dependsOnBean);

registerDependentBean(dependsOnBean, beanName);

}

}

// 这里又需要根据bean的类型分为三种情况：singleton、prototype、request/session

if(mbd.isSingleton()) {

//通过自定义ObjectFactory实例化Bean，此结果可能是半成品(是FactoryBean等)

sharedInstance = getSingleton(beanName, newObjectFactory() {

publicObjectgetObject() throws BeansException {

try{

//真正实例化装配的逻辑在createBean方法中

returncreateBean(beanName, mbd, args);

}

catch(BeansException ex) {

destroySingleton(beanName);

throwex;

}

}

});

//上一步半成品的Bean交给getObjectForBeanInstance方法处理

bean = getObjectForBeanInstance(sharedInstance, name, beanName, mbd);

}

elseif(mbd.isPrototype()) {

ObjectprototypeInstance = null;

try{

beforePrototypeCreation(beanName);

//真正实例化装配的逻辑在createBean方法中

prototypeInstance = createBean(beanName, mbd, args);

}

finally{

afterPrototypeCreation(beanName);

}

//上一步半成品的Bean交给getObjectForBeanInstance方法处理

bean = getObjectForBeanInstance(prototypeInstance, name, beanName, mbd);

}

else{

//request、session 的bean

StringscopeName = mbd.getScope();

final Scope scope = (Scope) this.scopes.get(scopeName);

if(scope == null) {

thrownewIllegalStateException( "No Scope registered for scope '"+ scopeName + "'");

}

try{

ObjectscopedInstance = scope.get(beanName, newObjectFactory() {

publicObjectgetObject() throws BeansException {

beforePrototypeCreation(beanName);

try{

//真正实例化装配的逻辑在createBean方法中

returncreateBean(beanName, mbd, args);

}

finally{

afterPrototypeCreation(beanName);

}

}

});

//上一步半成品的Bean交给getObjectForBeanInstance方法处理

bean = getObjectForBeanInstance(scopedInstance, name, beanName, mbd);

}

catch(IllegalStateException ex) {

thrownewBeanCreationException(beanName,

"Scope '"+ scopeName + "' is not active for the current thread; "+

"consider defining a scoped proxy for this bean if you intend to refer to it from a singleton",

ex);

}

}

}

if(requiredType != null&& bean != null&&

!requiredType.isAssignableFrom(bean.getClass())) {

thrownewBeanNotOfRequiredTypeException(name, requiredType, bean.getClass());

}

returnbean;

}

bean的加载经历了一个复杂的过程，上面代码主要做了以下几件事(此段摘抄自《Spring源码深度解析》)：

1.转换对应的beanName。如果name=“&aa”的，会去除&符号。或者<bean>标签带有alias（别名的意思），则取alias所表示最终的beanName。

2.尝试从缓存中加载单例bean。如果加载不成功，会再次尝试从singletonFactories中加载。

3.bean的实例化。假如我们需要对工厂bean进行处理，那么这里得到的其实是工厂bean 的初始状态。真正干活的则是getObjectForBeanInstance定义factory-method方法返回的bean。

4.原型模式的依赖检查。如果A类有B的属性，B中有A的属性，则会产生循环依赖。参考：spring如何解决循环依赖问题http://www.cnblogs.com/bhlsheji/p/5208076.html

5.将存储的Xml配置文件的GernericBeanDefinition转换为RootBeanDefinition。前文提到的用于承载属性的BeanDefinition有三个实现，GernericBeanDefinition，RootBeanDefinition和ChildBeanDefinition，如果父类bean不为空的话，这里会把所有的属性一并合并父类属性，因为后续所有的Bean都是针对RootBeanDefinition的。

6.寻找依赖。在初始化一个bean的时候，会首先初始化这个bean所对应的依赖。

7.根据不同的scope创建bean。scope属性默认是singleton，还有prototype、request等。

8.类型转换。如果bean是个String，而requiredType传入了Integer，然后返回bean，加载结束。

其中,最重要的步骤是(7),spring的常用特性都在那里实现.

4.FactoryBean

首先要分辨BeanFactory 与 FactoryBean的区别， 两个名字很像，所以容易搞混。这里做一个简单的比喻你就明白了：

1.FactoryBean：工厂类接口，用户可以通过实现该接口定制实例化 bean的逻辑。我们把bean比作是人，那么FactoryBean则是女娲，首先它本身有人的特征，但它能够生产人。

2.BeanFactory ：BeanFactory定义了 IOC 容器的最基本形式。如果bean还比作是人，那么它可以理解成三界，三界里有各种功能的人，它是一个容器，可以管理很多的人。

FactoryBean里干了什么事情？

publicinterfaceFactoryBean<T>{

//返回由FactoryBean创建的Bean实例,如果isSingleton返回true,则该实例会放到spring容器中单例缓存池中.

TgetObject()throwsException;

//返回FactoryBean创建的bean类型.

Class<?> getObjectType();

//返回由FactoryBean创建的bean实例的作用域是singleton还是prototype

booleanisSingleton();

}

它的作用不在这里做阐述，ref：Spring的FactoryBean使用http://www.cnblogs.com/quanyongan/p/4133724.html

写到这里，总结一下阅读Spring源码的心得:

1.学习Spring思想和编码规范。Spring的很多函数代码量大，逻辑复杂,而Spring的编码风格就是将复杂的逻辑分解，分成N个小函数的嵌套，每一层都是对下一层的总结和概要。在工作中最佩服的一个大神说过：学习Spring源码思想为我所用，哪怕是一天学习一个变量名，他在工作中设计很多小组件的时候都是基于Spring思想和规范。他说，不要迷茫学什么技术，其实每天只要进步一点点就好，突破的是自己，而不是某个领域。用10年其实才敢说入门一门技术。

2.跟了Spring代码的函数，你会或多或少发现一些规律：一个真正干活的函数其实是以do开头的，如doGetBean，而给我们错觉的函数，如getBean和createBean等等方法，其实只是从全局角度做一些统筹工作。

3.放弃阅读源码是一个不明智的选择，因为你失去了跟大师学习的机会。当你硬着头皮读完一个框架的源码，则其他框架都是相通的。

4.下一篇文章：Spring AOP是什么?你都拿它做什么?因为篇幅有限，AOP又是一个重要且内容比较多的部分，所以打算单独拿出来搞事情。