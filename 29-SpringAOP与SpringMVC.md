# Spring

## AOP

Aspect Oriented Programming 面向切面编程

Object Oriented Programming 面向对象编程

将程序中所有参与模块都抽象成对象，然后通过对象之间的相互调用完成特定的功能

AOP 是对 OOP 的一种补充，是在另外一个纬度上抽象出对象，具体是指程序运行时动态将非业务切入到业务代码中，实现了业务代码和非业务代码的解耦合。

```xml
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-context</artifactId>
  <version>5.3.23</version>
</dependency>

<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-aop</artifactId>
  <version>5.3.23</version>
</dependency>

<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-aspects</artifactId>
  <version>5.3.22</version>
</dependency>
```

```java
package com.southwind.aop;

public interface Cal {
    public int add(int num1,int num2);
    public int sub(int num1,int num2);
    public int mul(int num1,int num2);
    public int div(int num1,int num2);
}
```

```java
package com.southwind.aop;

import org.springframework.stereotype.Component;

@Component
public class CalImpl implements Cal {
    @Override
    public int add(int num1, int num2) {
        int result = num1 + num2;
        return result;
    }

    @Override
    public int sub(int num1, int num2) {
        int result = num1 - num2;
        return result;
    }

    @Override
    public int mul(int num1, int num2) {
        int result = num1 * num2;
        return result;
    }

    @Override
    public int div(int num1, int num2) {
        int result = num1 / num2;
        return result;
    }
}
```

```java
package com.southwind.aop;

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.context.annotation.EnableAspectJAutoProxy;
import org.springframework.stereotype.Component;

import java.util.Arrays;

@Component
@Aspect
@EnableAspectJAutoProxy
public class LoggerAspect {
    /**
     * 参数日志
     */
    @Before("execution(public int com.southwind.aop.CalImpl.*(..))")
    public void before(JoinPoint joinPoint){
        String name = joinPoint.getSignature().getName();
        String args = Arrays.toString(joinPoint.getArgs());
        System.out.println(name + "方法的参数是" + args);
    }

    @AfterReturning(value = "execution(public int com.southwind.aop.CalImpl.*(..))",returning = "result")
    public void afterReturn(JoinPoint joinPoint,Object result){
        String name = joinPoint.getSignature().getName();
        System.out.println(name + "方法的结果是" + result);
    }
}
```

```java
package com.southwind.aop;

import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class Test {
    public static void main(String[] args) {
        ApplicationContext applicationContext = new AnnotationConfigApplicationContext("com.southwind.aop");
        Cal cal = (Cal) applicationContext.getBean("calImpl");
        System.out.println(cal.add(1, 1));
        System.out.println(cal.sub(2, 1));
        System.out.println(cal.mul(3, 3));
        System.out.println(cal.div(10, 2));
    }
}
```

AOP 基于 IoC，将业务对象和切面对象分别注入到 IoC 中，同时业务对象只写业务代码，切面对象只写非业务代码，从而在代码层面实现业务代码和非业务代码的解耦合，通过 IoC 再将业务代码和非业务代码整合到一起即可，开发时解耦，运行时合并。

如何整合？通过代理对象，将业务对象注入到 IoC 中，将切面对象也注入到 IoC 中，由 IoC 负责根据业务对象和切面对象创建出一个动态代理对象，分别去完成业务代码和非业务代码的执行。

1、业务对象和切面对象必须注入到 IoC 中

2、切面对象需要添加切面注解以及动态代理注解

# Spring MVC

Spring MVC 是 Spring 全家桶的一款产品，对 Java Web 开发流程进行了封装，不需要再通过 Servlet 的方式来完成代码的开发，无论是代码量还是参数的接收，返回值等等各方面都做了优化。

Spring MVC 是基于 Spring 框架的

基于 IoC 容器

Servlet：

创建一个 Servlet，接收客户端请求，完成相关的业务操作

Spring MVC 基于 Servlet 的

请求进入到 Servlet，由 Servlet 再将这些请求分发到不同的 Java 类中，由各自的 Java 类处理具体的业务

rr

request -> response 

网页 数据

html json

1、导入相关依赖

```xml
<dependency>
  <groupId>org.springframework</groupId>
  <artifactId>spring-webmvc</artifactId>
  <version>5.3.27</version>
</dependency>
```

2、配置 web.xml

```xml
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
  <display-name>Archetype Created Web Application</display-name>

  <servlet>
    <servlet-name>springmvc</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:springmvc.xml</param-value>
    </init-param>
  </servlet>

  <servlet-mapping>
    <servlet-name>springmvc</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>

</web-app>
```

3、创建 springmvc 配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context.xsd
       http://www.springframework.org/schema/mvc
       http://www.springframework.org/schema/mvc/spring-mvc-3.2.xsd">

    <!-- 扫包 -->
    <context:component-scan base-package="com.southwind.controller"></context:component-scan>

    <!-- 配置视图解析 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/"></property>
        <property name="suffix" value=".jsp"></property>
    </bean>

</beans>
```

4、创建控制器

```java
package com.southwind.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
public class HelloController {

    @RequestMapping("/index")
    public String index(){
        System.out.println("index...");
        return "index";
    }

}
```