# Spring

Spring 框架是 Java 开发的行业标准

Spring 全家桶：Spring MVC、Spring Boot、Spring Cloud、Spring Data JPA、Spring Security、Spring AI

项目开发基于 Spring 框架，由 Spring 框架搭建项目的基础环境，在此环境上再添加其他的业务框架

# Maven 

Maven 帮助工程进行 jar 包管理以及父子工程构建的服务

jar 包自动管理

由 Maven 自动给程序导入 jar

pom.xml 中配置工程所需的 jar

Maven 有一个远程仓库，包含了所有的 jar，本地只需要连接到远程仓库，就可以自动将相应的 jar 包下载到本地，再自动导入到工程中

```xml
<mirror>
    <id>alimaven</id>
    <mirrorOf>central</mirrorOf>  
    <name>aliyun maven</name>
    <url>http://maven.aliyun.com/nexus/content/groups/public/</url>      
</mirror>
```

# Spring

pom.xml

```xml
<dependencies>
  <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>5.3.23</version>
  </dependency>
</dependencies>
```

Spring 不是业务层框架，它是构建业务层框架的框架

Spring 提供的是底层的容器，该容器来构建业务层框架

业务框架本质上也是由各个对象组成的，Spring 提供的容器就是管理这些对象的

IoC：控制反转

AOP：面向切面

Spring 的两大核心组件 IoC + AOP

## IoC

将对象的创建方式进行反转，由原来的手动 new 变成现在的 Spring 框架自动创建

lombok 自动创建 get、set、toString 等方法的工具

IoC 创建对象，只需要在配置文件中设置你要创建的对象即可

> 在 XML 文件中配置 Bean

```java
package com.southwind.test;

import lombok.Data;

@Data
public class User {
    private Integer id;
    private String name;
    private Address address;
}
```

```java
package com.southwind.test;

import lombok.Data;

@Data
public class Address {
    private Integer id;
    private String value;
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <bean id="user" class="com.southwind.test.User">
        <property name="id" value="1"></property>
        <property name="name" value="张三"></property>
        <property name="address" ref="address"></property>
    </bean>

    <bean id="address" class="com.southwind.test.Address">
        <property name="id" value="1"></property>
        <property name="value" value="软件园"></property>
    </bean>

</beans>
```

```java
package com.southwind.test;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Test {
    public static void main(String[] args) {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring.xml");
        System.out.println(applicationContext.getBean("user"));
    }
}
```

> 通过注解进行配置

```java
package com.southwind.test;

import lombok.Data;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

@Data
@Component
public class User {
    @Value("1")
    private Integer id;
    @Value("张三")
    private String name;
    @Autowired
    private Address address;
}
```

```java
package com.southwind.test;

import lombok.Data;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

@Data
@Component
public class Address {
    @Value("11")
    private Integer id;
    @Value("软件园")
    private String value;
}
```

```java
package com.southwind.test;

import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Test {
    public static void main(String[] args) {
//        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring.xml");
//        System.out.println(applicationContext.getBean("user"));
//        System.out.println(applicationContext.getBean("address"));
        ApplicationContext applicationContext = new AnnotationConfigApplicationContext("com.southwind.test");
        System.out.println(applicationContext.getBean("user"));
        System.out.println(applicationContext.getBean("address"));
    }
}
```

1、实体类必须添加注解

2、构建 IoC 的时候包必须覆盖实体类

3、一个类只能构造一个 bean

> 基于配置类

将基于 XML 和注解进行整合，创建一个配置类来替代 XML 文件

```java
package com.southwind.configuration;

import com.southwind.entity.Address;
import com.southwind.entity.User;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * 配置类相当于XML
 */
@Configuration
public class BeanConfiguration {

    @Bean
    public User user(){
        User user = new User();
        user.setId(1);
        user.setName("张三");
        Address address = new Address();
        address.setId(1);
        address.setValue("软件园");
        user.setAddress(address);
        return user;
    }

}
```

```java
package com.southwind.test;

import com.southwind.configuration.BeanConfiguration;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Test {
    public static void main(String[] args) {
//        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring.xml");
//        System.out.println(applicationContext.getBean("user"));
//        System.out.println(applicationContext.getBean("user2"));
//        ApplicationContext applicationContext = new AnnotationConfigApplicationContext("com.southwind");
//        System.out.println(applicationContext.getBean("user"));
//        System.out.println(applicationContext.getBean("address"));
        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(BeanConfiguration.class);
        System.out.println(applicationContext.getBean("user"));
    }
}
```



```java
package com.southwind.entity;

import lombok.Data;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

@Data
@Component
public class User {
    @Value("3")
    private Integer id;
    @Value("王五")
    private String name;
    @Autowired
    private Address address;
}
```

```java
package com.southwind.entity;

import lombok.Data;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

@Data
@Component
public class Address {
    @Value("11")
    private Integer id;
    @Value("软件园")
    private String value;
}
```

```java
package com.southwind.configuration;

import com.southwind.entity.Address;
import com.southwind.entity.User;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * 配置类相当于XML
 */
@Configuration
public class BeanConfiguration {
    @Autowired
    private Address address;
    @Autowired
    private User user;

    @Bean
    public User user(){
        User user = new User();
        user.setId(1);
        user.setName("张三");
        user.setAddress(address);
        return user;
    }

    @Bean(value = "test")
    public User user1(){
        User user = new User();
        user.setId(2);
        user.setName("李四");
        user.setAddress(address);
        return user;
    }

    @Bean
    public User user2(){
        return this.user;
    }

}
```

```java
package com.southwind.test;

import com.southwind.configuration.BeanConfiguration;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Test {
    public static void main(String[] args) {
//        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring.xml");
//        System.out.println(applicationContext.getBean("user"));
//        System.out.println(applicationContext.getBean("user2"));
//        ApplicationContext applicationContext = new AnnotationConfigApplicationContext("com.southwind");
//        System.out.println(applicationContext.getBean("user"));
//        System.out.println(applicationContext.getBean("address"));
        ApplicationContext applicationContext = new AnnotationConfigApplicationContext("com.southwind");
        System.out.println(applicationContext.getBean("user2"));
    }
}
```