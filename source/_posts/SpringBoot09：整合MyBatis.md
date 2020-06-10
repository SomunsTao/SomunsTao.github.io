---
title: SpringBoot09：整合MyBatis
date: 2020-03-09
tags: SpringBoot
categories: SpringBoot
keywords: spring
description: you can "just run"
---



#  整合MyBatis

官方文档：http://mybatis.org/spring-boot-starter/mybatis-spring-boot-autoconfigure/

Maven仓库地址：https://mvnrepository.com/artifact/org.mybatis.spring.boot/mybatis-spring-boot-starter/2.1.1

 ![img](https://img2020.cnblogs.com/i-beta/1418974/202003/1418974-20200316130241759-935776379.png)

##  整合测试

 1、导入 MyBatis 所需要的依赖

```
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.1</version>
</dependency>
```

2、配置数据库连接信息（不变）

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
spring:
  datasource:
    username: root
    password: 123456
    #?serverTimezone=UTC解决时区的报错
    url: jdbc:mysql://localhost:3306/springboot?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8
    driver-class-name: com.mysql.cj.jdbc.Driver
    type: com.alibaba.druid.pool.DruidDataSource

    #Spring Boot 默认是不注入这些属性值的，需要自己绑定
    #druid 数据源专有配置
    initialSize: 5
    minIdle: 5
    maxActive: 20
    maxWait: 60000
    timeBetweenEvictionRunsMillis: 60000
    minEvictableIdleTimeMillis: 300000
    validationQuery: SELECT 1 FROM DUAL
    testWhileIdle: true
    testOnBorrow: false
    testOnReturn: false
    poolPreparedStatements: true

    #配置监控统计拦截的filters，stat:监控统计、log4j：日志记录、wall：防御sql注入
    #如果允许时报错  java.lang.ClassNotFoundException: org.apache.log4j.Priority
    #则导入 log4j 依赖即可，Maven 地址：https://mvnrepository.com/artifact/log4j/log4j
    filters: stat,wall,log4j
    maxPoolPreparedStatementPerConnectionSize: 20
    useGlobalDataSourceStat: true
    connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=500
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

**3、测试数据库是否连接成功！**

**4、创建实体类，导入 Lombok！**

Department.java

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
package com.kuang.pojo;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
@AllArgsConstructor
public class Department {

    private Integer id;
    private String departmentName;

}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

**5、创建mapper目录以及对应的 Mapper 接口**

DepartmentMapper.java

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//@Mapper : 表示本类是一个 MyBatis 的 Mapper
@Mapper
@Repository
public interface DepartmentMapper {

    // 获取所有部门信息
    List<Department> getDepartments();

    // 通过id获得部门
    Department getDepartment(Integer id);

}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

**6、对应的Mapper映射文件**

DepartmentMapper.xml

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.kuang.mapper.DepartmentMapper">

    <select id="getDepartments" resultType="Department">
       select * from department;
    </select>

    <select id="getDepartment" resultType="Department" parameterType="int">
       select * from department where id = #{id};
    </select>

</mapper>
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

**7、maven配置资源过滤问题**

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
<resources>
    <resource>
        <directory>src/main/java</directory>
        <includes>
            <include>**/*.xml</include>
        </includes>
        <filtering>true</filtering>
    </resource>
</resources>
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

**8、编写部门的 DepartmentController 进行测试！**

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
@RestController
public class DepartmentController {
    
    @Autowired
    DepartmentMapper departmentMapper;
    
    // 查询全部部门
    @GetMapping("/getDepartments")
    public List<Department> getDepartments(){
        return departmentMapper.getDepartments();
    }

    // 查询全部部门
    @GetMapping("/getDepartment/{id}")
    public Department getDepartment(@PathVariable("id") Integer id){
        return departmentMapper.getDepartment(id);
    }
    
}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

**启动项目访问进行测试！**

## 我们增加一个员工类再测试下，为之后做准备

1、新建一个pojo类 Employee ；

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Employee {

    private Integer id;
    private String lastName;
    private String email;
    //1 male, 0 female
    private Integer gender;
    private Integer department;
    private Date birth;

    private Department eDepartment; // 冗余设计

}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

2、新建一个 EmployeeMapper 接口

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
//@Mapper : 表示本类是一个 MyBatis 的 Mapper
@Mapper
@Repository
public interface EmployeeMapper {

    // 获取所有员工信息
    List<Employee> getEmployees();

    // 新增一个员工
    int save(Employee employee);

    // 通过id获得员工信息
    Employee get(Integer id);

    // 通过id删除员工
    int delete(Integer id);

}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

3、编写 EmployeeMapper.xml 配置文件

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.kuang.mapper.EmployeeMapper">

    <resultMap id="EmployeeMap" type="Employee">
        <id property="id" column="eid"/>
        <result property="lastName" column="last_name"/>
        <result property="email" column="email"/>
        <result property="gender" column="gender"/>
        <result property="birth" column="birth"/>
        <association property="eDepartment"  javaType="Department">
            <id property="id" column="did"/>
            <result property="departmentName" column="dname"/>
        </association>
    </resultMap>

    <select id="getEmployees" resultMap="EmployeeMap">
        select e.id as eid,last_name,email,gender,birth,d.id as did,d.department_name as dname
        from department d,employee e
        where d.id = e.department
    </select>

    <insert id="save" parameterType="Employee">
        insert into employee (last_name,email,gender,department,birth)
        values (#{lastName},#{email},#{gender},#{department},#{birth});
    </insert>

    <select id="get" resultType="Employee">
        select * from employee where id = #{id}
    </select>

    <delete id="delete" parameterType="int">
        delete from employee where id = #{id}
    </delete>

</mapper>
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

4、编写EmployeeController类进行测试

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
@RestController
public class EmployeeController {

    @Autowired
    EmployeeMapper employeeMapper;

    // 获取所有员工信息
    @GetMapping("/getEmployees")
    public List<Employee> getEmployees(){
        return employeeMapper.getEmployees();
    }

    @GetMapping("/save")
    public int save(){
        Employee employee = new Employee();
        employee.setLastName("kuangshen");
        employee.setEmail("qinjiang@qq.com");
        employee.setGender(1);
        employee.setDepartment(101);
        employee.setBirth(new Date());
        return employeeMapper.save(employee);
    }

    // 通过id获得员工信息
    @GetMapping("/get/{id}")
    public Employee get(@PathVariable("id") Integer id){
        return employeeMapper.get(id);
    }

    // 通过id删除员工
    @GetMapping("/delete/{id}")
    public int delete(@PathVariable("id") Integer id){
        return employeeMapper.delete(id);
    }

}
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

测试结果完成，搞定收工！