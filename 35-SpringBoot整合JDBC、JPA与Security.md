# Spring Boot

Spring Boot 是脚手架框架，快速搭建 Java 项目

Spring Boot 可以快速将其他框架进行整合

## Spring Boot 整合 JdbcTemplate

JdbcTemplate 是一个轻量级的 JDBC 封装组件

### 什么是 JdbcTemplate？

是 Spring 自带的 JDBC 模板组件，底层实现了对 JDBC 的封装，需要开发者自定义 SQL 语句，JdbcTemplate 帮助我们完成数据库的连接，SQL 的执行，结果集的封装。

JdbcTemplate 提供了通用的 SQL 操作方法，execute、update、batchUpdate、query

JdbcTemplate 和 MyBatis 的区别？

MyBatis 的 SQL 定义在 XML 文件中，JdbcTemplate 的 SQL 定义在 Java 类中

1、pom.xml 中添加依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
```

2、创建 Mapper 接口，定义 CRUD 的方法

```java
package com.southwind.jdbctemplate;

import com.southwind.entity.News;

import java.util.List;

public interface NewsMapper {
    public List<News> list();
    public News getById(Integer id);
    public void add(News news);
    public void update(News news);
    public void deleteById(Integer id);
    public void batchAdd();
    public void batchUpdate();
    public void batchDelete();
}
```

```java
package com.southwind.jdbctemplate;

import com.southwind.entity.News;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.BeanPropertyRowMapper;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.stereotype.Repository;

import java.util.ArrayList;
import java.util.Date;
import java.util.List;

@Repository
public class NewsMapperImpl implements NewsMapper {

    @Autowired
    private JdbcTemplate jdbcTemplate;

    @Override
    public List<News> list() {
        return this.jdbcTemplate.query(
                "select * from sys_news"
        , new BeanPropertyRowMapper<>(News.class));
    }

    @Override
    public News getById(Integer id) {
        return this.jdbcTemplate.queryForObject(
                "select * from sys_news where id = ?"
        , new Object[]{id},
        new BeanPropertyRowMapper<>(News.class));

    }

    @Override
    public void add(News news) {
        this.jdbcTemplate.update(
              "insert into sys_news(title,content,createtime,opername) values(?,?,?,?)"
        , news.getTitle(),news.getContent(),news.getCreatetime(),news.getOpername());
    }

    @Override
    public void update(News news) {
        this.jdbcTemplate.update(
                "update sys_news set title = ?,content = ?,createtime = ?,opername = ? where id = ?"
        ,news.getTitle(),news.getContent(),news.getCreatetime()
        ,news.getOpername(),news.getId());
    }

    @Override
    public void deleteById(Integer id) {
        this.jdbcTemplate.update(
                "delete from sys_news where id = ?",
                id);
    }

    @Override
    public void batchAdd() {
        List<Object[]> args = new ArrayList<>();
        args.add(new Object[]{"测1","测11",new Date(),"张三"});
        args.add(new Object[]{"测2","测22",new Date(),"张三"});
        this.jdbcTemplate.batchUpdate(
                "insert into sys_news(title,content,createtime,opername) values(?,?,?,?)"
        ,args);
    }

    @Override
    public void batchUpdate() {
        List<Object[]> args = new ArrayList<>();
        args.add(new Object[]{"测12","测112",new Date(),"李四",53});
        args.add(new Object[]{"测22","测222",new Date(),"李四",54});
        this.jdbcTemplate.batchUpdate(
                "update sys_news set title = ?,content = ?,createtime = ?,opername = ? where id = ?"
        , args);
    }

    @Override
    public void batchDelete() {
        List<Object[]> args = new ArrayList<>();
        args.add(new Object[]{53});
        args.add(new Object[]{54});
        this.jdbcTemplate.batchUpdate(
                "delete from sys_news where id = ?"
        ,args);
    }
}
```

```java
package com.southwind.controller;

import com.southwind.entity.News;
import com.southwind.jdbctemplate.NewsMapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/news")
public class NewsController {

    @Autowired
    private NewsMapper newsMapper;

    @GetMapping("/list")
    public List<News> list(){
        return this.newsMapper.list();
    }

    @GetMapping("/getById/{id}")
    public News getById(@PathVariable("id") Integer id){
        News news = null;
        try {
            news = this.newsMapper.getById(id);
        } catch (Exception e) {
            return null;
        }
        return news;
    }

    @PostMapping("/add")
    public void add(@RequestBody News news){
        this.newsMapper.add(news);
    }

    @PutMapping("/update")
    public void update(@RequestBody News news){
        this.newsMapper.update(news);
    }

    @DeleteMapping("/delete/{id}")
    public void delete(@PathVariable("id") Integer id){
        this.newsMapper.deleteById(id);
    }

    @GetMapping("/batchAdd")
    public void batchAdd(){
        this.newsMapper.batchAdd();
    }

    @GetMapping("/batchUpdate")
    public void batchUpdate(){
        this.newsMapper.batchUpdate();
    }

    @GetMapping("/batchDelete")
    public void batchDelete(){
        this.newsMapper.batchDelete();
    }

}
```

## Spring Boot 整合 Spring Data JPA

### 什么是 Spring Data JPA？

Spring Data JPA 是 Spring 框架提供的持久层解决方案

1、pom.xml 引入相关依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
```

2、实体类

```java
package com.southwind.jpaentity;
import lombok.Data;

import javax.persistence.*;
import java.util.Date;

@Data
@Entity
public class News {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer id;
    @Column
    private String title;
    @Column
    private String content;
    @Column
    private Date createtime;
    @Column
    private String opername;
}
```

3、创建 Repository 接口

```java
package com.southwind.repository;

import com.southwind.jpaentity.News;
import org.springframework.data.jpa.repository.JpaRepository;

public interface NewsRepository extends JpaRepository<News,Integer> {
}
```

4、创建 Controller

```java
package com.southwind.controller;

import com.southwind.jpaentity.News;
import com.southwind.repository.NewsRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.util.List;
import java.util.Optional;

@RestController
@RequestMapping("/test")
public class TestController {

    @Autowired
    private NewsRepository newsRepository;

    @GetMapping("/list")
    public List<News> list(){
        return this.newsRepository.findAll();
    }

    @GetMapping("/getById/{id}")
    public News getById(@PathVariable("id") Integer id){
        Optional<News> optionalNews = this.newsRepository.findById(id);
        return optionalNews.get();
    }

    @PostMapping("/add")
    public void add(@RequestBody News news){
        this.newsRepository.save(news);
    }

    @PutMapping("/update")
    public void update(@RequestBody News news){
        this.newsRepository.save(news);
    }

    @DeleteMapping("/delete/{id}")
    public void delete(@PathVariable("id") Integer id){
        this.newsRepository.deleteById(id);
    }
}
```

## Spring Boot 整合 Spring Security

Spring Security、Shiro

1、pom.xml 引入相关依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

2、创建页面

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <h1>welcome</h1>
</body>
</html>
```

3、创建控制器

```java
package com.southwind.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class IndexController {

    @GetMapping("/welcome")
    public String index(){
        return "welcome";
    }

}
```

除了最基本的登录认证，还可以使用 Spring Security 来完成资源权限管理：当请求某个资源时，对角色进行验证，如果该角色拥有访问权限则正常访问，否则无法访问。

角色：ADMIN、USER

ADMIN 可以访问 index.html 和 admin.html

USER 只能访问 index.html

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <h1>index</h1>
</body>
</html>
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <h1>admin</h1>
</body>
</html>
```

```java
package com.southwind.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class IndexController {

    @GetMapping("/welcome")
    public String welcome(){
        return "welcome";
    }

    @GetMapping("/admin")
    public String admin(){
        return "admin";
    }

    @GetMapping("/index")
    public String index(){
        return "index";
    }

}
```

```java
package com.southwind.configuration;

import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

@Configuration
@EnableWebSecurity
public class SecurityConfiguration extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
                .antMatchers("/admin").hasRole("ADMIN")
                .antMatchers("/index").access("hasRole('ADMIN') or hasRole('USER')")
                .anyRequest().authenticated()
                .and()
                .formLogin()
                .permitAll()
                .and()
                .logout()
                .permitAll()
                .and()
                .csrf()
                .disable();
    }

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication().passwordEncoder(new PasswordEncoderImpl())
                .withUser("user").password(new PasswordEncoderImpl()
                .encode("123456")).roles("USER")
                .and()
                .withUser("admin").password(new PasswordEncoderImpl()
                .encode("123456")).roles("ADMIN","USER");
    }
}
```

```java
package com.southwind.configuration;

import org.springframework.security.crypto.password.PasswordEncoder;

public class PasswordEncoderImpl implements PasswordEncoder {
    @Override
    public String encode(CharSequence rawPassword) {
        return rawPassword.toString();
    }

    @Override
    public boolean matches(CharSequence rawPassword, String encodedPassword) {
        return encodedPassword.equals(rawPassword.toString());
    }
}
```