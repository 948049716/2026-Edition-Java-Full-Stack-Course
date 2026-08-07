# Java 概述

Java 是 IT 研发类招聘需求量最大的语言之一

为什么 Java 需求量最大？

Java 主要做 Web 端服务器开发

99.9 % 项目都是基于 Web 

Web 服务器端 Java 是第一语言

C#、PHP、Java、Python、Go

生态的区别，Java 的生态其他语言无法对抗

基于 Java 衍生出来的各种框架、服务、组件......

Spring 框架

# 开发版本

JDK 21

企业主流的 JDK 仍然是 8

分布式事务 阿里的 seata ，只支持 JDK 8

Spring Boot 2.X

MySQL 8

Redis 缓存数据库 NoSQL

Spring Cloud 微服务

Java 基础 打基础 单机

JavaWeb 用 Java 做 Web 开发，Servlet ，Java EE

框架 

Spring 全家桶

Spring、SSM、MyBatis Plus、Shiro、Spring Boot、Spring Cloud

实操 做项目

面试题 简历

# JDK

JDK：Java Development Kit Java 开发工具包

JRE：Java Runtime Environment Java 运行环境

只需要运行 Java 项目，安装 JRE

需要开发 Java 项目，必须安装 JDK，包含 JRE

Java 不是解释型语言

HTML JS 

Java 需要先编译成字节码文件，再通过 JVM（Java 虚拟机）对字节码文件进行进一步的解析，生成机器码，底层操作系统才能识别机器码进行程序的运行

Java 为什么先需要编译？

为了实现 Java 的一大特性，一次编译，到处运行 跨平台

同一套代码可以在不同的操作系统上运行

如何实现跨平台？

通过编译的方式

Java 源代码编译成字节码文件

字节码文件分别放到不同的操作系统中，由 JVM 将它们各自解析成对应操作系统的机器码进行执行

Test.java ->Test.class ->运行

Java 源代码编译成字节码的命令是什么？

```
javac Test.java
```

Java 字节码运行的命令是什么？

```
java Test
```

都可以省略，由开发工具帮助我们来完成

# 变量

为什么要使用变量？变量是为了解决什么问题？

数据存储地址的问题

数据都是在内存中进行存储的，存在一个问题？

程序会随机分配内存区域

内存地址是 16 进制的数据，随机分配，地址很记住

如何解决这个问题？

使用变量

变量就是为了解决内存地址很难记忆的问题

相当于给内存地址起了一个别名，取数据的时候直接通过变量名取即可，不需要再通过内存地址来取数据了







