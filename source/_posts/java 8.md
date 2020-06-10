---
title: java 8 新特性
date: 2020-3-21
tags: java
categories: java
keywords: java
description: java8 新特性
---



# [java8 新特性精心整理(全)](https://www.cnblogs.com/xichji/p/11570387.html) 转载

## 前言

越来越多的项目已经使用 [Java 8](http://www.oracle.com/technetwork/java/javase/8train-relnotes-latest-2153846.html) 了，毫无疑问，[Java 8](http://www.oracle.com/technetwork/java/javase/8train-relnotes-latest-2153846.html) 是Java自Java 5（发布于2004年）之后的最重要的版本。这个版本包含语言、编译器、库、工具和 JVM 等方面的十多个新特性。在本文中我们将学习这些新特性，并用实际的例子说明在什么场景下适合使用。

**引用：**本文参考了这两篇文章，加以自己的理解，整理成一份最容易理解的 Java8 新特性文章，有少部分章节可能内容一致，但绝对不是抄袭，只是为了文章的完整性，大部分常用的地方加了我自己的理解和示例。

https://blog.csdn.net/yczz/article/details/50896975

https://blog.csdn.net/maosijunzi/article/details/38658095

## 适合读者及目标

### 目标人群

- 适合有用过 lambda 表达式的同学，想彻底了解清楚
- 学习 Java8 的新特定

### 目标

- 了解 java8 的函数式接口和 Lambda 表达式
- 方法引用的使用
- 接口的静态方法和默认方法
- Date/Time Api 的使用
- Stream API 的使用

## 1. Java 语言的新特性

Java8 的 lambda 的使用确实方便了许多，但也使初次了解的人感觉到难以阅读，其实是你不习惯的原因。很多语言从一开始就支持了 Lambda 表达式，像 Groovy,Scala 等。

### 1.1 Lambda 表达式和函数式接口

在 Java8 以前，我们想要让一个方法可以与用户进行交互，比如说使用方法内的局部变量；这时候就只能使用接口做为参数，让用户实现这个接口或使用匿名内部类的形式，把局部变量通过接口方法传给用户。

**传统匿名内部类缺点：**代码臃肿，难以阅读

#### Lambda 表达式

Lambda 表达式将函数当成参数传递给某个方法，或者把代码本身当作数据处理；

**语法格式：**

- 用逗号分隔的参数列表
- `->` 符号
- 和 语句块 组成

```
Arrays.asList( "a", "b", "d" ).forEach( e -> System.out.println( e ) );
```

等价于

```
List<String> list = Arrays.asList( "a", "b", "d" );
for(String e:list){
    System.out.println(e);
}
```

如果语句块比较复杂，使用 `{}` 包起来

```
Arrays.asList( "a", "b", "d" ).forEach( e -> {
    String m = "9420 "+e;
    System.out.print( m );
});
```

Lambda 本质上是匿名内部类的改装，所以它使用到的变量都会隐式的转成 `final` 的

```
String separator = ",";
Arrays.asList( "a", "b", "d" ).forEach( 
    e -> System.out.print( e + separator ) );
```

等价于

```
final String separator = ",";
Arrays.asList( "a", "b", "d" ).forEach( 
    e -> System.out.print( e + separator ) );
```

Lambda 的返回值和参数类型由编译器推理得出，不需要显示定义，如果只有一行代码可以不写 **return** 语句

```
Arrays.asList( "a", "b", "d" ).sort( ( e1, e2 ) -> e1.compareTo( e2 ) );
```

等价于

```
List<String> list = Arrays.asList("a", "b", "c");
Collections.sort(list, new Comparator<String>() {
    @Override
    public int compare(String o1, String o2) {
        return o1.compareTo(o2);
    }
});
```

#### 函数式接口

- 接口中只能有一个接口方法
- 可以有静态方法和默认方法
- 使用 `@FunctionalInterface` 标记
- 默认方法可以被覆写

```
@FunctionalInterface
public interface FunctionalDefaultMethods {
    void method();
 
    default void defaultMethod() {            
    }
    
    static void staticMethod(){
    }
}
private interface Defaulable {
    // Interfaces now allow default methods, the implementer may or 
    // may not implement (override) them.
    default String notRequired() { 
        return "Default implementation"; 
    }        
}
 
private static class DefaultableImpl implements Defaulable {
}
 
private static class OverridableImpl implements Defaulable {
    @Override
    public String notRequired() {
        return "Overridden implementation";
    }
}

// 也可以由接口覆盖 
public interface OverridableInterface extends Defaulable{
    @Override
    public String notRequired() {
        return "interface Overridden implementation";
    }
}
```

由于JVM上的默认方法的实现在字节码层面提供了支持，因此效率非常高。默认方法允许在不打破现有继承体系的基础上改进接口。该特性在官方库中的应用是：给 **java.util.Collection**接口添加新方法，如 **stream()**、**parallelStream()**、**forEach()**和**removeIf()** 等等。

##### 已经存在的 Java8 定义的函数式接口

我们基本不需要定义自己的函数式接口，Java8 已经给我们提供了大量的默认函数式接口，基本够用，在 `rt.jar` 包的 `java.util.function` 目录下可以看到所有默认的函数式接口，大致分为几类

- `Function` **T** 作为输入，返回的 **R** 作为输出
- `Predicate` **T** 作为输入 ，返回 **boolean** 值的输出
- `Consumer` **T** 作为输入 ，没有输出
- `Supplier` 没有输入 , **R** 作为输出
- `BinaryOperator` **两个 T** 作为输入 ，**T** 同样是输出
- `UnaryOperator` 是 `Function` 的变种 ，输入输出者是 **T**

其它的都是上面几种的各种扩展，只为更方便的使用，下面演示示例，你可以把其当成正常的接口使用，由用户使用 Lambda 传入。

```
// hello world 示例
Function<String,String> function = (x) -> {return x+"Function";};
System.out.println(function.apply("hello world"));  // hello world Function

UnaryOperator<String> unaryOperator = x -> x + 2;
System.out.println(unaryOperator.apply("9420-"));   // 9420-2

// 判断输入值是否为偶数示例
Predicate<Integer> predicate = (x) ->{return x % 2 == 0 ;};
System.out.println(predicate.test(1));              // false

// 这个没有返回值
Consumer<String> consumer = (x) -> {System.out.println(x);};
consumer.accept("hello world ");                    // hello world

// 这个没有输入 
Supplier<String> supplier = () -> {return "Supplier";};
System.out.println(supplier.get());                 // Supplier

// 找出大数
BinaryOperator<Integer> bina = (x, y) ->{return x > y ? x : y;};
bina.apply(1,2);                                    // 2 
```

### 1.2 方法引用

方法引用使得开发者可以直接引用现存的方法、Java类的构造方法或者实例对象。方法引用和Lambda表达式配合使用，使得java类的构造方法看起来紧凑而简洁，没有很多复杂的模板代码。

```
public static class Car {
    public static Car create( final Supplier< Car > supplier ) {
        return supplier.get();
    }              
 
    public static void collide( final Car car ) {
        System.out.println( "Collided " + car.toString() );
    }
 
    public void follow( final Car another ) {
        System.out.println( "Following the " + another.toString() );
    }
 
    public void repair() {   
        System.out.println( "Repaired " + this.toString() );
    }
}
```

第一种方法引用的类型是构造器引用，语法是**Class::new**，或者更一般的形式：**Class::new**。注意：这个构造器没有参数。

```
final Car car = Car.create( Car::new );
```

等价于

```
Car car = Car.create(() -> new Car());
```

第二种方法引用的类型是静态方法引用，语法是**Class::static_method**。注意：这个方法接受一个Car类型的参数。

```
cars.forEach( Car::collide );
```

`forEach` 原型为 `forEach(Consumer action)` 使用的是 **Consumer** 只有参数，没有返回值；这个参数 T 就是 car 类型，因为是 `cars.forEach` 嘛，所以上面的方法引用等价于

```
cars.forEach(car -> Car.collide(car));
```

第三种方法引用的类型是某个类的成员方法的引用，语法是**Class::method**，注意，这个方法没有定义入参：

```
cars.forEach( Car::repair );
```

它等价于

```
cars.forEach(car -> car.repair());
```

### 1.3 重复注解

自从Java 5中引入[注解](http://www.javacodegeeks.com/2012/08/java-annotations-explored-explained.html)以来，这个特性开始变得非常流行，并在各个框架和项目中被广泛使用。不过，注解有一个很大的限制是：在同一个地方不能多次使用同一个注解。Java 8打破了这个限制，引入了重复注解的概念，允许在同一个地方多次使用同一个注解。

在Java 8中使用 **@Repeatable** 注解定义重复注解，实际上，这并不是语言层面的改进，而是编译器做的一个trick，底层的技术仍然相同。可以利用下面的代码说明：

```
@Target( ElementType.TYPE )
@Retention( RetentionPolicy.RUNTIME )
@Repeatable( Filters.class )
public @interface Filter {
    String value();
};

@Filter( "filter1" )
@Filter( "filter2" )
public interface Filterable {        
}

public static void main(String[] args) {
    for( Filter filter: Filterable.class.getAnnotationsByType( Filter.class ) ) {
        System.out.println( filter.value() );
    }
}
```

正如我们所见，这里的**Filter**类使用 `@Repeatable(Filters.class)` 注解修饰，而**Filters**是存放**Filter**注解的容器，编译器尽量对开发者屏蔽这些细节。这样，**Filterable**接口可以用两个**Filter**注解注释（这里并没有提到任何关于Filters的信息）。

另外，反射API提供了一个新的方法：**getAnnotationsByType()**，可以返回某个类型的重复注解，例如`Filterable.class.getAnnoation(Filters.class)`将返回两个Filter实例。

### 1.4 更好的类型推断

Java 8编译器在类型推断方面有很大的提升，在很多场景下编译器可以推导出某个参数的数据类型，从而使得代码更为简洁。例子代码如下：

```
public class Value< T > {
    public static< T > T defaultValue() { 
        return null; 
    }
 
    public T getOrDefault( T value, T defaultValue ) {
        return ( value != null ) ? value : defaultValue;
    }
}
public class TypeInference {
    public static void main(String[] args) {
        final Value< String > value = new Value<>();
        value.getOrDefault( "22", Value.defaultValue() );
    }
}
```

参数 **Value.defaultValue()** 的类型由编译器推导得出，不需要显式指明。在Java 7中这段代码会有编译错误，除非使用`Value.defaultValue()`

### 1.5 拓宽注解的应用场景

Java 8拓宽了注解的应用场景。现在，注解几乎可以使用在任何元素上：局部变量、接口类型、超类和接口实现类，甚至可以用在函数的异常定义上。下面是一些例子：

```
package com.javacodegeeks.java8.annotations;
 
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import java.util.ArrayList;
import java.util.Collection;
 
public class Annotations {
    @Retention( RetentionPolicy.RUNTIME )
    @Target( { ElementType.TYPE_USE, ElementType.TYPE_PARAMETER } )
    public @interface NonEmpty {        
    }
 
    public static class Holder< @NonEmpty T > extends @NonEmpty Object {
        public void method() throws @NonEmpty Exception {            
        }
    }
 
    @SuppressWarnings( "unused" )
    public static void main(String[] args) {
        final Holder< String > holder = new @NonEmpty Holder< String >();        
        @NonEmpty Collection< @NonEmpty String > strings = new ArrayList<>();        
    }
}
```

**ElementType.TYPE_USER** 和 **ElementType.TYPE_PARAMETER** 是Java 8新增的两个注解，用于描述注解的使用场景。Java 语言也做了对应的改变，以识别这些新增的注解。

## 2. Java 编译器的新特性

Java 8 开始正式支持参数名称，终于不需要读 class 字节码来获取参数名称了，这对于经常使用反射的人特别有用。

在 Java8 这个特性默认是关闭的，需要开启参数才能获取参数名称：

```
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.1</version>
    <configuration>
        <compilerArgument>-parameters</compilerArgument>
        <source>1.8</source>
        <target>1.8</target>
    </configuration>
</plugin>
```

## 3. JVM 的新特性

使用**Metaspace**（[JEP 122](http://openjdk.java.net/jeps/122)）代替持久代（**PermGen** space）。在JVM参数方面，使用**-XX:MetaSpaceSize**和**-XX:MaxMetaspaceSize**代替原来的**-XX:PermSize**和**-XX:MaxPermSize**。

## 4. Java 官方库的新特性

Java 8增加了很多新的工具类（date/time类），并扩展了现存的工具类，以支持现代的并发编程、函数式编程等，本章节参考原文，并提取出常用功能。

### 4.1 Streams

Streams 操作分为中间操作和晚期操作，中间操作会返回一个新的 Stream ，只是把要做的操作记录起来而已，并不会真的执行，晚期操作才会真的遍历列表并执行所有操作

Stream 的另一个价值就是支持了并行处理 `parallel` 方法。

Stream API 简化了集合的操作，并扩展了集合的分组，求和，mapReduce，flatMap ，排序等功能，下面列出项目中经常用到的功能，会以使用频率排序。

1. 准备一个用于下面例子测试的对象

```
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
@AllArgsConstructor
public class Vehicle {
    //车架号
    private String vin;
    // 车主手机号
    private String phone;
    // 车主姓名
    private String name;
    // 所属车租车公司
    private Integer companyId;
    // 个人评分
    private Double score;
    //安装的设备列表imei,使用逗号分隔
    private String deviceNos;
}
```

1. 准备一些车辆数据

```
static List<Vehicle> vehicles = new ArrayList<>();

@Before
public void init(){
    List<String> imeis = new ArrayList<>();
    for (int i = 0; i <5 ; i++) {
        List<String> singleVehicleDevices = new ArrayList<>();
        for (int j = 0; j < 3; j++) {
            String imei = RandomStringUtils.randomAlphanumeric(15);
            singleVehicleDevices.add(imei);
        }
        imeis.add(StringUtils.join(singleVehicleDevices,','));
    }
    vehicles.add(new Vehicle("KPTSOA1K67P081452","17620411498","9420",1,4.5,imeis.get(0)));
    vehicles.add(new Vehicle("KPTCOB1K18P057071","15073030945","张玲",2,1.4,imeis.get(1)));
    vehicles.add(new Vehicle("KPTS0A1K87P080237","19645871598","sanri1993",1,3.0,imeis.get(2)));
    vehicles.add(new Vehicle("KNAJC526975740490","15879146974","李种",1,3.9,imeis.get(3)));
    vehicles.add(new Vehicle("KNAJC521395884849","13520184976","袁绍",2,4.9,imeis.get(4)));
}
```

#### 4.1.1 forEach 遍历Collection 数据

```
vehicles.forEach(vehicle -> System.out.println(vehicle));

//这样就可以遍历打印
vehicles.forEach(System.out::println);
```

#### 4.1.2 forEach 遍历 Map 数据

```
Map<String,Integer> map = new HashMap<>();
map.put("a",1);map.put("b",2);map.put("c",3);

map.forEach((k,v) -> System.out.println("key:"+k+",value:"+v));
```

#### 4.1.3 filter 数据过滤

```
// 去掉评分为 3 分以下的车
List<Vehicle> collect = vehicles.stream().filter(vehicle -> vehicle.getScore() >= 3).collect(Collectors.toList());
```

#### 4.1.4 map 对象映射

对一个 `List` 大部分情况下，我们只需要列表中的某一列，或者需要把里面的每一个对象转换成其它的对象，这时候可以使用 map 映射,示例：

```
// 取出所有的车架号列表
 List<String> vins = vehicles.stream().map(Vehicle::getVin).collect(Collectors.toList());
```

#### 4.1.5 groupBy 按照某个属性进行分组

```
// 按照公司 Id 进行分组
Map<Integer, List<Vehicle>> companyVehicles = vehicles.stream().collect(Collectors.groupingBy(Vehicle::getCompanyId));

// 按照公司分组求司机打分和
Map<Integer, Double> collect = vehicles.stream().collect(Collectors.groupingBy(Vehicle::getCompanyId, Collectors.summingDouble(Vehicle::getScore)));
```

#### 4.1.6 sort 按照某个属性排序 ，及多列排序

```
// 单列排序 
vehicles.sort((v1,v2) -> v2.getScore().compareTo(v1.getScore()));

// 或使用 Comparator 类来构建比较器，流处理不会改变原列表，需要接收返回值才能得到预期结果
 List<Vehicle> collect = vehicles.stream().sorted(Comparator.comparing(Vehicle::getScore).reversed()).collect(Collectors.toList());

// 多列排序，score 降序，companyId 升序排列
List<Vehicle> collect = vehicles.stream().sorted(Comparator.comparing(Vehicle::getScore).reversed()
                .thenComparing(Comparator.comparing(Vehicle::getCompanyId)))
                .collect(Collectors.toList());
```

#### 4.1.7 flatMap 扁平化数据处理

```
// 查出所有车绑定的所有设备
List<String> collect = vehicles.stream().map(vehicle -> {
    String deviceNos = vehicle.getDeviceNos();
    return StringUtils.split(deviceNos,',');
}).flatMap(Arrays::stream).collect(Collectors.toList());
```

flatMap 很适合 `List` 或 `List` 这种结构，可以当成一个列表来处理；像上面的设备列表，在数据库中存储的结构就是以逗号分隔的数据，而车辆列表又是一个列表数据。

#### 4.1.8 mapReduce 数据处理

```
// 对所有司机的总分求和
Double reduce = vehicles.stream().parallel().map(Vehicle::getScore).reduce(0d, Double::sum);
```

#### 4.1.9 综合处理示例

```
// 总的分值
Double totalScore = vehicles.stream().parallel().map(Vehicle::getScore).reduce(0d, Double::sum);

// 查看每一个司机占的分值比重
List<String> collect = vehicles.stream()
    .mapToDouble(vehicle -> vehicle.getScore() / totalScore)
    .mapToLong(weight -> (long) (weight * 100))
    .mapToObj(percentage -> percentage + "%")
    .collect(Collectors.toList());
```

原文的 boxed 不知道是什么意思，希望有大神能帮忙解答下，不用 boxed 也是可以的

### 4.2 Optional

Optional 用来解决 Java 中经常出现的 **NullPointerException** ，从而避免源码被各种空检查污染，使源码更加简洁和更加容易阅读

```
// 假设有一个对象 obj ,你不知道它是不是为空的，但是你想用它的方法，可以这么玩
Optional<T> canUseObj = Optional.ofNullable(obj);
canUseObj.ifPresent(System.out::println);       //如果 obj 不为空，则可以使用 obj 的方法，这里做个简单输出 
```

### 4.3 Date/Time API(JSR 310)

新的日期时间工具全部都在 `java.time` 及其子包中。

#### 4.3.1 新 Date/Time API 设计原则

Java 8日期/时间API是 [JSR-310](https://jcp.org/en/jsr/detail?id=310) 规范的实现，它的目标是克服旧的日期/时间API实现中所有的缺陷，新的日期/时间API的一些设计原则如下：

- 不变性：新的日期/时间API中，所有的类都是不可变的，这种设计有利于并发编程。
- 关注点分离：新的API将人可读的日期时间和机器时间（unix timestamp）明确分离，它为日期（Date）、时间（Time）、日期时间（DateTime）、时间戳（unix timestamp）以及时区定义了不同的类。
- 清晰：在所有的类中，方法都被明确定义用以完成相同的行为。举个例子，要拿到当前实例我们可以使用now()方法，在所有的类中都定义了format()和parse()方法，而不是像以前那样专门有一个独立的类。为了更好的处理问题，所有的类都使用了工厂模式和策略模式，一旦你使用了其中某个类的方法，与其他类协同工作并不困难。
- 实用操作：所有新的日期/时间API类都实现了一系列方法用以完成通用的任务，如：加、减、格式化、解析、从日期/时间中提取单独部分等操作。
- 可扩展性：新的日期/时间API是工作在ISO-8601日历系统上的，但我们也可以将其应用在非IOS的日历上。

#### 4.3.2 常用类及其使用

时间大致可以分为三个部分：日期、时间、时区

其中日期又细分为年、月、日；时间又细分为时、分、秒

一般机器时间用从 1970-01-01T00:00 到现在的秒数来表示时间; 这里纠正大部分人犯的一个错误概念，时间戳指的是秒数，而不是毫秒数。

几乎所有的时间对象都实现了 `Temporal` 接口，所以接口参数一般都是 `Temporal`

- **Instant：** 表示时间线上的一个点，参考点是标准的Java纪元(epoch)，即1970-01-01T00：00：00Z（1970年1月1日00:00 GMT）
- **LocalDate：** 日期值对象如 2019-09-22
- **LocalTime：**时间值对象如 21:25:36
- **LocalDateTime：**日期+时间值对象
- **ZoneId：**时区
- **ZonedDateTime：**日期+时间+时区值对象
- **DateTimeFormatter：**用于日期时间的格式化
- **Period：**用于计算日期间隔
- **Duration：**用于计算时间间隔

##### 4.3.2.1 `Instant` 表示时间线上的一个点(瞬时)

```
// 测试执行一个 new 操作使用的时间(纳秒值)
Instant begin = Instant.now();
StreamMain streamMain = new StreamMain();
Instant end = Instant.now();
System.out.println(Duration.between(begin,end).toNanos());
```

##### 4.3.2.2 `LocalDate`、`LocalTime`、`LocalDateTime`、`ZonedDateTime` 可以规为一组，用于表示时间的

```
// 可以使用 of 方法构建它们的实例,如下面创建了一个 2019-9-22 21:42:59 东八区 的时间对象 
LocalDate localDate = LocalDate.of(2019, Month.SEPTEMBER, 22);
LocalTime localTime = LocalTime.of(21, 42, 59);
LocalDateTime localDateTime = LocalDateTime.of(localDate, localTime);
ZonedDateTime zonedDateTime = ZonedDateTime.of(localDateTime, ZoneId.systemDefault());

// 获取现在的时间，这是一个静态方法
LocalDate now = LocalDate.now();

// 每个实例可以获取它们的 part 信息,如获取年 
int year = localDate.getYear();

// 可以修改 part 信息，这将返回一个新对象，如增加一年
LocalDate localDatePlus = localDate.plusYears(1);

// 设置 part 信息，也会返回新的对象，如设置为 2017 年 
LocalDate localDateWithYear = localDate.withYear(2017);

// 比较两个日期 isAfter,isBefore
boolean after = localDate.isAfter(LocalDate.now());

// 格式化日期时间
// yyyy-MM-dd
System.out.println(now.format(DateTimeFormatter.ISO_DATE));
// yyyy-MM-ddTHH:mm:ss
System.out.println(now.format(DateTimeFormatter.ISO_DATE_TIME));
// yyyy-MM-dd HH:mm:ss
System.out.println(now.format(DateTimeFormatter.ofLocalizedDateTime(FormatStyle.MEDIUM)));

// 日期解析 
System.out.println(LocalDate.parse("2019-09-22"));
System.out.println(LocalDateTime.parse("2019-09-22T21:05:22"));
System.out.println(LocalDateTime.parse("2019-09-22 21:05:22",DateTimeFormatter.ofLocalizedDateTime(FormatStyle.MEDIUM)));
```

##### 4.3.2.3 `ZoneId` 用来操作时区，它提供了获取所有时区和本地时区的方法

```
ZoneId zoneId = ZoneId.systemDefault();
Set<String> availableZoneIds = ZoneId.getAvailableZoneIds();
```

##### 4.3.2.4 `Period`，`Duration` 可以视为一组，用于计算时间间隔

```
// 创建一个两周的间隔
Period periodWeeks = Period.ofWeeks(2);

// 一年三个月零二天的间隔
Period custom = Period.of(1, 3, 2);

// 一天的时长
Duration duration = Duration.ofDays(1);

// 计算2015/6/16 号到现在 2019/09/22 过了多久，它这个把间隔分到每个 part 了
LocalDate now = LocalDate.now();
LocalDate customDate = LocalDate.of(2015, 6, 16);
Period between = Period.between(customDate, now);
// 结果为 4:3:6 即过去了 4年3个月6天了
System.out.println(between.getYears()+":"+between.getMonths()+":"+between.getDays());

// 比较两个瞬时的时间间隔 
Instant begin = Instant.now();
Instant end = Instant.now();
Duration.between(begin,end);

// 同样可以修改 part 信息和设置 part 信息，都是返回新的对象来表示设置过的值，原来的对象不变
Period plusDays = between.plusDays(1);
Period withDays = between.withDays(4);
```

### 4.4 Base64

对于 Base64 终于不用引用第三方包了，使用 java 库就可以完成

```
// 编码
final String encoded = Base64.getEncoder().encodeToString( text.getBytes( StandardCharsets.UTF_8 ) );
// 解码
final String decoded = new String( Base64.getDecoder().decode( encoded ),StandardCharsets.UTF_8 );
```

### 4.5 JUC 工具包扩充

基于新增的lambda表达式和steam特性，为Java 8中为**java.util.concurrent.ConcurrentHashMap**类添加了新的方法来支持聚焦操作；另外，也为**java.util.concurrentForkJoinPool**类添加了新的方法来支持通用线程池操作（更多内容可以参考[我们的并发编程课程](http://academy.javacodegeeks.com/course/java-concurrency-essentials/)）。

Java 8还添加了新的**java.util.concurrent.locks.StampedLock**类，用于支持基于容量的锁——该锁有三个模型用于支持读写操作（可以把这个锁当做是**java.util.concurrent.locks.ReadWriteLock**的替代者）。

在**java.util.concurrent.atomic**包中也新增了不少工具类，列举如下：

- DoubleAccumulator
- DoubleAdder
- LongAccumulator
- LongAdder

## 5. 新的工具

Java 8提供了一些新的命令行工具，这部分会讲解一些对开发者最有用的工具。

### 5.1 类依赖分析器：jdeps

**deps**是一个相当棒的命令行工具，它可以展示包层级和类层级的Java类依赖关系，它以**.class**文件、目录或者Jar文件为输入，然后会把依赖关系输出到控制台。

我们可以利用jedps分析下[Spring Framework库](https://blog.csdn.net/yczz/article/details/50896975)，为了让结果少一点，仅仅分析一个JAR文件：**org.springframework.core-3.0.5.RELEASE.jar**。

```
jdeps org.springframework.core-3.0.5.RELEASE.jar
```

这个命令会输出很多结果，我们仅看下其中的一部分：依赖关系按照包分组，如果在classpath上找不到依赖，则显示"not found".

```
org.springframework.core-3.0.5.RELEASE.jar -> C:\Program Files\Java\jdk1.8.0\jre\lib\rt.jar
   org.springframework.core (org.springframework.core-3.0.5.RELEASE.jar)
      -> java.io                                            
      -> java.lang                                          
      -> java.lang.annotation                               
      -> java.lang.ref                                      
      -> java.lang.reflect                                  
      -> java.util                                          
      -> java.util.concurrent                               
      -> org.apache.commons.logging                         not found
      -> org.springframework.asm                            not found
      -> org.springframework.asm.commons                    not found
   org.springframework.core.annotation (org.springframework.core-3.0.5.RELEASE.jar)
      -> java.lang                                          
      -> java.lang.annotation                               
      -> java.lang.reflect                                  
      -> java.util
```

## 一点小推广

创作不易，希望可以支持下我的开源软件，及我的小工具，欢迎来 gitee 点星，fork ，提 bug 。

Excel 通用导入导出，支持 Excel 公式
博客地址：https://blog.csdn.net/sanri1993/article/details/100601578
gitee：https://gitee.com/sanri/sanri-excel-poi

使用模板代码 ，从数据库生成代码 ，及一些项目中经常可以用到的小工具
博客地址：https://blog.csdn.net/sanri1993/article/details/98664034
gitee：https://gitee.com/sanri/sanri-tools-maven