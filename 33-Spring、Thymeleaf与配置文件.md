# Spring 

SSM 框架整合完成之后，需要进行大量的配置，Spring IoC 来完成，完成 Spring MVC 和 MyBatis 所需对象的创建

Java Web 启动机制，通过反射进行创建 DispatcherServlet、ContextLoaderListener，web.xml

IoC 创建 dataSource、sqlSessionFactory、MapperScannerConfigurer、InternalResourceViewResolver、Mapper 动态代理对象

Java 是面向对象的编程语言

手动配置 bean 步骤繁琐、出错率高，如何优化？

Spring Boot 框架应运而生

Spring 手动配置 web.xml、spring.xml、config.xml、springmvc.xml 

Spring Boot 不需要进行任何通用配置，不需要任何的 XML 文件，自动配置

Spring MVC、MyBatis 的基础组件，个性化的配置仍然需要

Spring Boot 自动装配

基于 Spring Boot，在此基础上添加各种第三方框架 Spring MVC、MyBatis、MyBatis Plus、Spring Security、Spring Data JPA

Spring + Spring MVC + MyBatis

Spring Boot + Spring MVC + MyBatis

Thymeleaf 前端模板，HTML

不能直接通过 url 来访问 templates 路径下的资源，Thymeleaf 模板必须经过 Controller 才能加载

url -》Controller -》Thymeleaf

```java
package com.southwind.controller;

import com.southwind.entity.News;
import com.southwind.mapper.NewsMapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;

import java.util.Date;
import java.util.List;

@Controller
public class HelloController {

    @Autowired
    private NewsMapper newsMapper;

    @RequestMapping("/list")
    public String list(Model model){
        List<News> list = this.newsMapper.list();
        model.addAttribute("list", list);
        return "index";
    }

    @GetMapping("/add")
    public String add(){
        return "add";
    }

    @PostMapping("/add")
    public String add(News news){
        news.setCreatetime(new Date());
        this.newsMapper.add(news);
        return "redirect:/list";
    }

    @GetMapping("/get")
    public String get(Integer id,Model model){
        News news = this.newsMapper.getById(id);
        model.addAttribute("news", news);
        return "edit";
    }

    @PostMapping("/update")
    public String update(News news){
        this.newsMapper.update(news);
        return "redirect:/list";
    }

    @GetMapping("/delete")
    public String delete(Integer id){
        this.newsMapper.delete(id);
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
import org.apache.ibatis.annotations.Delete;
import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Select;
import org.apache.ibatis.annotations.Update;

import java.util.List;

public interface NewsMapper {

    @Select({"select * from sys_news"})
    public List<News> list();

    @Insert({"insert into sys_news(title,content,createtime,opername) values(#{title},#{content},#{createtime},#{opername})"})
    public void add(News news);

    @Select({"select * from sys_news where id = #{id}"})
    public News getById(Integer id);

    @Update({"update sys_news set title = #{title},content = #{content},opername=#{opername} where id = #{id}"})
    public void update(News news);

    @Delete({"delete from sys_news where id = #{id}"})
    public void delete(Integer id);
}
```

```java
package com.southwind;

import org.mybatis.spring.annotation.MapperScan;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
@MapperScan("com.southwind.mapper")
public class Myspringboot001Application {

    public static void main(String[] args) {
        SpringApplication.run(Myspringboot001Application.class, args);
    }

}
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form action="/add" method="post">
        <table>
            <tr>
                <td>Title：</td>
                <td>
                    <input type="text" name="title"/>
                </td>
            </tr>
            <tr>
                <td>Content：</td>
                <td>
                    <input type="text" name="content"/>
                </td>
            </tr>
            <tr>
                <td>Opername：</td>
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

```html
<!DOCTYPE html>
<html lang="en">
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form action="update" method="post">
        <table>
            <tr>
                <td>ID：</td>
                <td>
                    <input type="text" name="id" th:value="${news.id}" readonly/>
                </td>
            </tr>
            <tr>
                <td>Title：</td>
                <td>
                    <input type="text" name="title" th:value="${news.title}"/>
                </td>
            </tr>
            <tr>
                <td>Content：</td>
                <td>
                    <input type="text" name="content" th:value="${news.content}"/>
                </td>
            </tr>
            <tr>
                <td>Createtime：</td>
                <td>
                    <input type="text" name="createtime" th:value="${news.createtime}" readonly/>
                </td>
            </tr>
            <tr>
                <td>Opername：</td>
                <td>
                    <input type="text" name="opername" th:value="${news.opername}"/>
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

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <a href="/add">添加</a>
    <table>
        <tr>
            <td>ID</td>
            <td>标题</td>
            <td>内容</td>
            <td>创建时间</td>
            <td>操作员</td>
            <td>操作</td>
        </tr>
        <tr th:each="news:${list}">
            <td th:text="${news.id}"></td>
            <td th:text="${news.title}"></td>
            <td th:text="${news.content}"></td>
            <td th:text="${news.createtime}"></td>
            <td th:text="${news.opername}"></td>
            <td>
                <a th:href="'/get?id='+${news.id}">编辑</a>
                <a th:href="'/delete?id='+${news.id}">删除</a>
            </td>
        </tr>
    </table>
</body>
</html>
```

```yaml
server:
  port: 8181
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/car_rental_separate
    username: root
    password: root
```

# Tymeleaf 

text 用于显示文本信息

```java
@GetMapping("/test")
public String index(Model model){
    model.addAttribute("name", "张三");
    return "test";
}
```

```html
<h1 th:text="${name}"></h1>
```

if 用来判断内容是否显示

```java
@GetMapping("/test")
public String index(Model model){
    model.addAttribute("name", "张三");
    model.addAttribute("score", 86);
    return "test";
}
```

```html
<h1 th:if="${score>=90}">优秀</h1>
<h1 th:if="${score<90 && score>80}">良好</h1>
```

each 用来遍历集合

```java
List<News> list = this.newsMapper.list();
model.addAttribute("list", list);
return "index";
```

```html
<tr th:each="news:${list}">
    <td th:text="${news.id}"></td>
    <td th:text="${news.title}"></td>
    <td th:text="${news.content}"></td>
    <td th:text="${news.createtime}"></td>
    <td th:text="${news.opername}"></td>
    <td>
        <a th:href="'/get?id='+${news.id}">编辑</a>
        <a th:href="'/delete?id='+${news.id}">删除</a>
    </td>
</tr>
```

value 用来给标签赋值

```java
@GetMapping("/get")
public String get(Integer id,Model model){
    News news = this.newsMapper.getById(id);
    model.addAttribute("news", news);
    return "edit";
}
```

```html
<table>
    <tr>
        <td>ID：</td>
        <td>
            <input type="text" name="id" th:value="${news.id}" readonly/>
        </td>
    </tr>
    <tr>
        <td>Title：</td>
        <td>
            <input type="text" name="title" th:value="${news.title}"/>
        </td>
    </tr>
    <tr>
        <td>Content：</td>
        <td>
            <input type="text" name="content" th:value="${news.content}"/>
        </td>
    </tr>
    <tr>
        <td>Createtime：</td>
        <td>
            <input type="text" name="createtime" th:value="${news.createtime}" readonly/>
        </td>
    </tr>
    <tr>
        <td>Opername：</td>
        <td>
            <input type="text" name="opername" th:value="${news.opername}"/>
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
```

# application 配置文件

Spring Boot 工程的配置文件名称必须是 application，格式有两种，分别是 properties 和 yaml，如果同时存在两个格式的配置文件，优先级更高的是 properties。

properties

```properties
server.port=8181
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/car_rental_separate
spring.datasource.username=root
spring.datasource.password=root
```

```yaml
server:
  port: 8383
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/car_rental_separate
    username: root
    password: root
```