# SSM 框架整合

SSM：Spring + Spring MVC + MyBatis

Spring Boot 只是替换了 Spring

Spring Boot + Spring MVC + MyBaits

Spring 和 Spring Boot 不是业务框架，只是整合其他业务框架的框架，基础容器

在基础容器上，整合各类业务框架，进行项目的开发

Spring 起了什么作用，IoC 容器自动创建各类对象

InputSteam、SqlSessionFactoryBuilder、SqlSessionFactory、SqlSession

DispatcherServlet、InternalResourceViewResolver

SSM 框架整合本质上就是将 MyBatis 、Spring MVC 所需要的各自对象组件的创建交给 Spring IoC 来完成

Spring 起到的作用就是整合创建另外两个框架所需要的对象

Spring MVC 负责 Web 层的交互，请求响应

MyBatis 负责持久层的交互，连接数据库

Spring Boot 就是优化 Spring

Spring 帮助 MyBatis Spring MVC 创建对象，通过 XML 配置文件的方式来创建对象

在 Spring Boot 中不需要任何的配置文件，自动创建对象，不需要再手动配置对象了

Spring 手动配置对象，Spring Boot 自动配置对象，不需要手动配

## 整合步骤

1、pom.xml

```xml
<dependencies>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.11</version>
        <scope>test</scope>
    </dependency>

    <!-- Spring MVC -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>5.2.7.RELEASE</version>
    </dependency>

    <!-- Spring JDBC -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-jdbc</artifactId>
        <version>5.2.7.RELEASE</version>
    </dependency>

    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.2.7.RELEASE</version>
    </dependency>

    <!-- MyBatis -->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.5</version>
    </dependency>

    <!-- MyBatis 整合 Spring -->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis-spring</artifactId>
        <version>2.0.3</version>
    </dependency>

    <!-- MySQL -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.21</version>
    </dependency>

    <!-- C3P0 -->
    <dependency>
        <groupId>com.mchange</groupId>
        <artifactId>c3p0</artifactId>
        <version>0.9.5.5</version>
    </dependency>

    <!-- JSTL -->
    <dependency>
        <groupId>jstl</groupId>
        <artifactId>jstl</artifactId>
        <version>1.2</version>
    </dependency>

    <!-- ServletAPI -->
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
        <version>4.0.1</version>
    </dependency>

    <!-- lombok -->
    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <version>1.18.12</version>
    </dependency>

</dependencies>
```

2、web.xml 配置 Spring、Spring MVC

```xml
<web-app>
  <display-name>Archetype Created Web Application</display-name>
  <!-- Spring -->
  <context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:spring.xml</param-value>
  </context-param>
  <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>
  
  <!-- Spring MVC -->
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

3、配置框架各自的配置文件

spring.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="user" value="root"></property>
        <property name="password" value="root"></property>
        <property name="driverClass" value="com.mysql.cj.jdbc.Driver"></property>
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/test11"></property>
        <property name="initialPoolSize" value="5"></property>
        <property name="maxPoolSize" value="10"></property>
    </bean>

    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"></property>
        <property name="mapperLocations" value="classpath:com/southwind/mapper/*.xml"></property>
        <property name="configLocation" value="classpath:config.xml"></property>
    </bean>

    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.southwind.mapper"></property>
    </bean>

</beans>
```

config.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <settings>
        <!-- 打印SQL-->
        <setting name="logImpl" value="STDOUT_LOGGING" />
        <setting name="lazyLoadingEnabled" value="true"/>
        <setting name="cacheEnabled" value="true"/>
    </settings>

</configuration>
```

springmvc.xml

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

## CRUD

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ page isELIgnored="false" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <a href="add.jsp">添加</a>
    <table border="1">
        <tr>
            <td>ID</td>
            <td>标题</td>
            <td>内容</td>
            <td>创建时间</td>
            <td>操作员</td>
            <td>操作</td>
        </tr>
        <c:forEach items="${list}" var="news">
            <tr>
                <td>${news.id}</td>
                <td>${news.title}</td>
                <td>${news.content}</td>
                <td>${news.time}</td>
                <td>${news.opername}</td>
                <td>
                    <a href="/get?id=${news.id}">编辑</a>
                    <a href="/delete?id=${news.id}">删除</a>
                </td>
            </tr>
        </c:forEach>
    </table>
</body>
</html>
```

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <form action="/add" method="post">
        <table>
            <tr>
                <td>title：</td>
                <td>
                    <input type="text" name="title"/>
                </td>
            </tr>
            <tr>
                <td>content：</td>
                <td>
                    <input type="text" name="content"/>
                </td>
            </tr>
            <tr>
                <td>opername：</td>
                <td>
                    <input type="text" name="opername"/>
                </td>
            </tr>
            <tr>
                <td>
                    <input type="submit" value="提交"/>
                </td>
                <td>
                    <input type="reset" value="重置"/>
                </td>
            </tr>
        </table>
    </form>
</body>
</html>
```

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ page isELIgnored="false" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <form action="/update" method="post">
        <table>
            <tr>
                <td>ID：</td>
                <td>
                    <input type="text" name="id" value="${news.id}" readonly/>
                </td>
            </tr>
            <tr>
                <td>title：</td>
                <td>
                    <input type="text" name="title" value="${news.title}"/>
                </td>
            </tr>
            <tr>
                <td>content：</td>
                <td>
                    <input type="text" name="content" value="${news.content}"/>
                </td>
            </tr>
            <tr>
                <td>createtime：</td>
                <td>
                    <input type="text" name="createtime" value="${news.createtime}" readonly/>
                </td>
            </tr>
            <tr>
                <td>opername：</td>
                <td>
                    <input type="text" name="opername" value="${news.opername}"/>
                </td>
            </tr>
            <tr>
                <td>
                    <input type="submit" value="提交"/>
                </td>
                <td>
                    <input type="reset" value="重置"/>
                </td>
            </tr>
        </table>
    </form>
</body>
</html>
```

```java
package com.southwind.controller;

import com.southwind.entity.News;
import com.southwind.mapper.NewsMapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.List;

@Controller
public class HelloController {

    @Autowired
    private NewsMapper newsMapper;

    @RequestMapping("/list")
    public String list(Model model){
        List<News> list = this.newsMapper.list();
        for (News news : list) {
            SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd");
            String format = simpleDateFormat.format(news.getCreatetime());
            news.setTime(format);
        }
        model.addAttribute("list", list);
        return "index";
    }

    @RequestMapping("/add")
    public String add(News news){
        news.setCreatetime(new Date());
        this.newsMapper.add(news);
        return "redirect:/list";
    }

    @RequestMapping("/get")
    public String get(Integer id,Model model){
        News news = this.newsMapper.getById(id);
        model.addAttribute("news", news);
        return "edit";
    }

    @RequestMapping("/update")
    public String update(News news){
        this.newsMapper.update(news);
        return "redirect:/list";
    }

    @RequestMapping("/delete")
    public String delete(Integer id){
        this.newsMapper.deleteById(id);
        return "redirect:/list";
    }
}
```

```java
package com.southwind.entity;

import lombok.Data;

import java.util.Date;

@Data
public class News {
    private Integer id;
    private String title;
    private String content;
    private Date createtime;
    private String opername;
    private String time;
}
```

```java
package com.southwind.mapper;

import com.southwind.entity.News;

import java.util.List;

public interface NewsMapper {
    public List<News> list();
    public void add(News news);
    public News getById(Integer id);
    public void update(News news);
    public void deleteById(Integer id);
}
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.southwind.mapper.NewsMapper">

    <select id="list" resultType="com.southwind.entity.News">
        select * from sys_news
    </select>

    <insert id="add" parameterType="com.southwind.entity.News">
        insert into sys_news(title,content,createtime,opername) values (#{title},#{content},#{createtime},#{opername})
    </insert>

    <select id="getById" parameterType="java.lang.Integer" resultType="com.southwind.entity.News">
        select * from sys_news where id = #{id}
    </select>

    <update id="update" parameterType="com.southwind.entity.News">
        update sys_news set title = #{title},content = #{content},opername = #{opername} where id = #{id}
    </update>

    <delete id="deleteById" parameterType="java.lang.Integer">
        delete from sys_news where id = #{id}
    </delete>

</mapper>
```