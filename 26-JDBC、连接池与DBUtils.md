# JDBC

Java Database Connectivity 是一个独立于特定数据库系统的，通用的 SQL 数据库存取和操作的公共接口

JDBC 不仅仅针对于一种数据库，针对于多种数据库，不同的数据库都可以使用 JDBC 来进行管理

Java -> MySQL 适用的接口 -》MySQL

Java -》Oracle 使用的接口 -》Oracle

Java -》 JDBC -》MySQL、Oracle、SQLServer

JDBC 分为两部分

- 面向应用的 API，开发者调用，接口
- 面向数据库的 API，共开发商开发数据库驱动程序

## JDBC API

DriverManager 类：管理各种不同的 JDBC 驱动

Connection 接口：连接 MySQL 数据库

Statement 接口：执行 SQL 语句

ResultSet 接口：接收结果集

## JDBC 的原理

1、加载数据库驱动，Java 程序和数据库的桥梁

Java -》insert ->MySQL 驱动 -》MySQL 的 SQL 语句

​						-》Oracle 驱动 -》Oracle 的 SQL 语句

​						-》SQLServer 驱动 -》SQLServer 的 SQL 语句

2、获取 Connection，一次连接

3、创建 Statement，执行 SQL

4、ReusltSet 接收结果集

常见错误：

Unknown database：数据库名称写错了

Access denied for user 'root1'@'localhost' (using password: YES)：用户名或密码错误

No suitable driver found for jdbc:mysql://localhost:3306/mytest1：数据库驱动未导入

CRUD：

- Query 查询
- Update 增删改

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;

public class Test {
    public static void main(String[] args) throws Exception {
        //连接数据库
        //url、username、password
        String url = "jdbc:mysql://localhost:3306/mytest1";
        String user = "root";
        String pwd = "root";
        Connection connection = DriverManager.getConnection(url, user, pwd);
        //创建 SQL
        String sql = "insert into person(name,money) values(?,?)";
        PreparedStatement statement = connection.prepareStatement(sql);
        statement.setString(1,"小明");
        statement.setInt(2, 500);
        int i = statement.executeUpdate();
        System.out.println(i);
        String sql = "update person set name = ?,money = ? where id = ?";
        PreparedStatement statement = connection.prepareStatement(sql);
        statement.setInt(3,4);
        statement.setInt(2, 666);
        statement.setString(1, "测试");
        int i = statement.executeUpdate();
        System.out.println(i);
        String sql = "delete from person where id = ?";
        PreparedStatement statement = connection.prepareStatement(sql);
        statement.setInt(1, 4);
        int i = statement.executeUpdate();
        System.out.println(i);
        String sql = "select * from person where id = ?";
        PreparedStatement statement = connection.prepareStatement(sql);
        statement.setInt(1, 3);
        ResultSet resultSet = statement.executeQuery();
        boolean next = resultSet.next();
        if(next){
            int anInt = resultSet.getInt(1);
            String string = resultSet.getString(2);
            int anInt1 = resultSet.getInt(3);
            System.out.println(anInt);
            System.out.println(string);
            System.out.println(anInt1);
        }
        
        String sql = "select * from person";
        PreparedStatement statement = connection.prepareStatement(sql);
        ResultSet resultSet = statement.executeQuery();
        while (resultSet.next()){
            int anInt = resultSet.getInt(1);
            String string = resultSet.getString(2);
            int anInt1 = resultSet.getInt(3);
            System.out.println(anInt);
            System.out.println(string);
            System.out.println(anInt1);
        }
    }
}
```

## JDBC 处理事务

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;

public class Test {
    public static void main(String[] args) throws Exception {
        //连接数据库
        //url、username、password
        String url = "jdbc:mysql://localhost:3306/mytest1";
        String user = "root";
        String pwd = "root";
        Connection connection = DriverManager.getConnection(url, user, pwd);
        //关闭自动提交
        connection.setAutoCommit(false);
        //创建 SQL
        try {
            String sql = "update person set money = ? where id = ?";
            PreparedStatement statement = connection.prepareStatement(sql);
            statement.setInt(1, 1500);
            statement.setInt(2, 1);
            statement.executeUpdate();
            System.out.println(10 / 10);
            PreparedStatement statement1 = connection.prepareStatement(sql);
            statement1.setInt(1, 500);
            statement1.setInt(2, 2);
            statement1.executeUpdate();
        } catch (Exception e){
            //事务回滚
            connection.rollback();
        }
        //手动提交
        connection.commit();
    }
}
```

## 数据库连接池

字符串常量池、线程池、数据库连接池，都是池化思想，重复使用资源，做到资源的合理分配

C3P0

```java
import com.mchange.v2.c3p0.ComboPooledDataSource;

import java.sql.Connection;

public class Test2 {
    public static void main(String[] args) throws Exception {
        ComboPooledDataSource dataSource = new ComboPooledDataSource();
        dataSource.setDriverClass("com.mysql.cj.jdbc.Driver");
        dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/mytest1");
        dataSource.setUser("root");
        dataSource.setPassword("root");
        dataSource.setInitialPoolSize(20);
        dataSource.setMaxPoolSize(40);
        dataSource.setMinPoolSize(2);
        dataSource.setAcquireIncrement(5);
        Connection connection = dataSource.getConnection();
    }
}
```

## DBUtils 工具

直接使用原生的 JDBC 查询数据，需要手动封装，步骤繁琐，可以借助于 DBUtils 工具帮助我们进行数据的封装。

```java
import com.mchange.v2.c3p0.ComboPooledDataSource;
import org.apache.commons.dbutils.QueryRunner;
import org.apache.commons.dbutils.handlers.BeanHandler;
import org.apache.commons.dbutils.handlers.BeanListHandler;

import java.sql.Connection;
import java.util.List;

public class Test3 {
    public static void main(String[] args) throws Exception {
        ComboPooledDataSource dataSource = new ComboPooledDataSource();
        dataSource.setDriverClass("com.mysql.cj.jdbc.Driver");
        dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/mytest1");
        dataSource.setUser("root");
        dataSource.setPassword("root");
        dataSource.setInitialPoolSize(20);
        dataSource.setMaxPoolSize(40);
        dataSource.setMinPoolSize(2);
        dataSource.setAcquireIncrement(5);
        Connection connection = dataSource.getConnection();

        QueryRunner queryRunner = new QueryRunner();
        String sql = "select * from person where id = ?";
        Person person = queryRunner.query(connection, sql, new BeanHandler<>(Person.class), 1);
        System.out.println(person);

        String sql1 = "select * from person";
        List<Person> list = queryRunner.query(connection, sql1, new BeanListHandler<>(Person.class));
        for (Person person1 : list) {
            System.out.println(person1);
        }
    }
}
```