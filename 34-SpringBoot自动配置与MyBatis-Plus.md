# Spring Boot

Spring 和 Spring Boot 都是用来构建 Java 工程的脚手架

Spring 和 Spring Boot 的区别？

Spring 需要手动进行组件的配置

Spring Boot 是自动进行组件的配置

## Spring Boot 如何自动进行组件的配置

自动装配

引入了 MyBatis 的依赖，项目启动时会自动加载 MyBatis 相关的组件

DataSource、sqlSessionFactory...

当启动 Spring Boot 的时候，pom.xml 中配置的所有框架的组件都会自动进行加载

如何进行自动装配

1、pom.xml 中添加 MyBatis 的依赖

2、Spring Boot 就会自动加载 MyBatis 需要的 bean

配置类：Spring 框架提供的一种特殊的类，专门用来配置（创建）各种 bean（对象）

只需要在该配置类中将需要创建的对象进行配置即可，Spring 框架就会读取配置类，进而创建这些对象（反射）

Spring Boot 如何自动实现 bean 的创建，通过配置类的方式

引入 MyBatis 依赖之后，依赖中会包含一个配置类的信息，配置类中会标注要创建的 bean

Spring Boot 启动之后会读取配置类信息，从而获取到 MyBatis 需要创建的 bean，然后进行创建

文件路径：META-INF/spring.factories

```yacas
# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.mybatis.spring.boot.autoconfigure.MybatisLanguageDriverAutoConfiguration,\
org.mybatis.spring.boot.autoconfigure.MybatisAutoConfiguration
```

key:org.springframework.boot.autoconfigure.EnableAutoConfiguration

value:org.mybatis.spring.boot.autoconfigure.MybatisLanguageDriverAutoConfiguration

org.mybatis.spring.boot.autoconfigure.MybatisAutoConfiguration

Spring  Boot 启动的时候，会读取 spring.factories 文件，通过 key 获取到两个配置类

1、MybatisLanguageDriverAutoConfiguration

2、MybatisAutoConfiguration

## MyBatis Plus

实际开发中会使用 MyBatis Plus 来替代 MyBatis

MyBatis Plus 是基于 MyBatis 进行了更加细致的封装，进一步减少代码量

MyBatis 需要开发者手动定义 SQL，MyBatis Plus 可以自动生成 SQL 语句，不需要开发者手动编写

MyBatis Plus 的依赖

```xml
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.4.3.1</version>
</dependency>
```

MyBatis Plus 除了可以自动生成 SQL 之外，还可以自动生成代码（Controller、Service、Mapper、Entity）

MyBatis Plus 逆向工程的依赖

```xml
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-generator</artifactId>
    <version>3.3.2</version>
</dependency>

<dependency>
    <groupId>org.apache.velocity</groupId>
    <artifactId>velocity</artifactId>
    <version>1.7</version>
</dependency>
```

Main

```java
package com.southwind;

import com.baomidou.mybatisplus.annotation.DbType;
import com.baomidou.mybatisplus.generator.AutoGenerator;
import com.baomidou.mybatisplus.generator.config.DataSourceConfig;
import com.baomidou.mybatisplus.generator.config.GlobalConfig;
import com.baomidou.mybatisplus.generator.config.PackageConfig;
import com.baomidou.mybatisplus.generator.config.StrategyConfig;
import com.baomidou.mybatisplus.generator.config.rules.NamingStrategy;

public class Main {
    public static void main(String[] args) {
        AutoGenerator autoGenerator = new AutoGenerator();
        //配置数据源
        DataSourceConfig dataSourceConfig = new DataSourceConfig();
        dataSourceConfig.setDbType(DbType.MYSQL);
        dataSourceConfig.setDriverName("com.mysql.cj.jdbc.Driver");
        dataSourceConfig.setUsername("root");
        dataSourceConfig.setPassword("root");
        dataSourceConfig.setUrl("jdbc:mysql://localhost:3306/dbname?useUnicode=true&characterEncoding=UTF-8");
        autoGenerator.setDataSource(dataSourceConfig);
        //全局配置
        GlobalConfig globalConfig = new GlobalConfig();
        globalConfig.setOpen(false);
        globalConfig.setOutputDir(System.getProperty("user.dir")+"/src/main/java");
        globalConfig.setServiceName("%sService");
        autoGenerator.setGlobalConfig(globalConfig);
        //设置包
        PackageConfig packageConfig = new PackageConfig();
        packageConfig.setParent("com.southwind");
        packageConfig.setEntity("entity");
        packageConfig.setMapper("mapper");
        packageConfig.setController("controller");
        packageConfig.setService("service");
        packageConfig.setServiceImpl("service.impl");
        autoGenerator.setPackageInfo(packageConfig);
        //生成策略
        StrategyConfig strategyConfig = new StrategyConfig();
        strategyConfig.setEntityLombokModel(true);
        strategyConfig.setInclude("sys_news");
        strategyConfig.setNaming(NamingStrategy.underline_to_camel);
        autoGenerator.setStrategy(strategyConfig);
        //启动
        autoGenerator.execute();
    }
}
```

