# Spring MVC

## Spring MVC 传参数

Spring MVC 会自动对 request 中的参数进行解析，类型转换，开发者只需要在方法定义处声明需要的参数类型格式，Spring MVC 会自动对参数进行解析和转换。

## Spring MVC 返回

1、视图，定义方法返回值类型为 String，返回视图名称，同时结合视图解析器，将真正的视图资源返回给调用者。

2、数据 JSON，需要在方法定义处添加 @RequestBody，方法中直接返回数据即可

返回视图是前后端不分离的单体架构，返回 JSON 数据前后端分离的架构

## 单体架构

1、返回视图

2、将业务数据同时带到前端

# MyBatis

MyBatis 是当前主流的 ORM 框架

1、pom.xml

```xml
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.4.5</version>
</dependency>

<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.30</version>
</dependency>
```

2、config.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>

    <settings>
        <!-- 打印SQL-->
        <setting name="logImpl" value="STDOUT_LOGGING" />
    </settings>
    
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"></transactionManager>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/car_rental_separate"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>
    
    <mappers>
        <mapper resource="com/southwind/mapper/NewsMapper.xml"></mapper>
    </mappers>

</configuration>
```

3、创建 Mapper 接口

```java
package com.southwind.mapper;

import com.southwind.entity.News;

public interface NewsMapper {
    public int add(News news);
}
```

4、接口对应的 Mapper.xml 

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.southwind.mapper.NewsMapper">

    <insert id="add" parameterType="com.southwind.entity.News">
        insert into sys_news(title,content,createtime,opername) values(#{title},#{content},#{createtime},#{opername})
    </insert>

</mapper>
```

5、使用

```java
package com.southwind;

import com.southwind.entity.News;
import com.southwind.mapper.NewsMapper;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.InputStream;
import java.util.Date;

public class Test {
    public static void main(String[] args) {
        InputStream resourceAsStream = Test.class.getClassLoader().getResourceAsStream("config.xml");
        SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
        SqlSessionFactory sqlSessionFactory = builder.build(resourceAsStream);
        SqlSession sqlSession = sqlSessionFactory.openSession();
        //获取Mapper的代理对象
        NewsMapper mapper = sqlSession.getMapper(NewsMapper.class);
        News news = new News();
        news.setTitle("测试");
        news.setContent("测试测试测试");
        news.setCreatetime(new Date());
        news.setOpername("admin");
        int add = mapper.add(news);
        System.out.println(add);
        sqlSession.commit();
    }
}
```

也可以将 SQL 直接添加到业务方法上

```java
package com.southwind.mapper;

import com.southwind.entity.News;
import org.apache.ibatis.annotations.Insert;

public interface NewsMapper {

    @Insert({"insert into sys_news(title,content,createtime,opername) values(#{title},#{content},#{createtime},#{opername})"})
    public int add(News news);
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

    @Insert({"insert into sys_news(title,content,createtime,opername) values(#{title},#{content},#{createtime},#{opername})"})
    public int add(News news);

    @Update({"update sys_news set title = #{title},content = #{content},createtime=#{createtime},opername=#{opername} where id=#{id}"})
    public int update(News news);

    @Delete({"delete from sys_news where id = #{id} "})
    public int delete(Integer id);

    @Select({"select * from sys_news where id = #{id}"})
    public News getById(Integer id);

    @Select({"select * from sys_news"})
    public List<News> list();
}
```

原始数据：identity opername

业务逻辑：创建 customer 对象，将 identity opername 的值分别赋给 customer 对象

## 多表关联查询

```java
package com.southwind.entity;

import lombok.Data;

import java.util.Date;

@Data
public class Rent {
    private String rentid;
    private Integer price;
    private Date begindate;
    private Date returndate;
    private String carnumber;
    private Date createtime;
    private Customer customer;
}
```

```java
package com.southwind.entity;

import lombok.Data;

import java.util.Date;

@Data
public class Customer {
    private String identity;
    private String custname;
    private String address;
    private String phone;
    private String career;
}
```

```java
package com.southwind.mapper;

import com.southwind.entity.Rent;
import org.apache.ibatis.annotations.Select;

public interface RentMapper {

    public Rent getById(String rentid);
}
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.southwind.mapper.RentMapper">

    <resultMap id="rentMap" type="com.southwind.entity.Rent">
        <id column="rentid" property="rentid"></id>
        <result column="price" property="price"></result>
        <result column="begindate" property="begindate"></result>
        <result column="returndate" property="returndate"></result>
        <result column="carnumber" property="carnumber"></result>
        <result column="createtime" property="createtime"></result>
        <association property="customer" javaType="com.southwind.entity.Customer">
            <id column="identity" property="identity"></id>
            <result column="identity" property="identity"></result>
            <result column="opername" property="custname"></result>
            <result column="address" property="address"></result>
            <result column="phone" property="phone"></result>
            <result column="career" property="career"></result>
        </association>
    </resultMap>

    <select id="getById" resultMap="rentMap">
        select * from bus_rent,bus_customer where bus_rent.identity = bus_customer.identity and rentid = #{rentid}
    </select>

</mapper>
```

```java
package com.southwind;

import com.southwind.entity.News;
import com.southwind.entity.Rent;
import com.southwind.mapper.NewsMapper;
import com.southwind.mapper.RentMapper;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.InputStream;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.List;

public class Test {
    public static void main(String[] args) {
        InputStream resourceAsStream = Test.class.getClassLoader().getResourceAsStream("config.xml");
        SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
        SqlSessionFactory sqlSessionFactory = builder.build(resourceAsStream);
        SqlSession sqlSession = sqlSessionFactory.openSession();
        //获取Mapper的代理对象
        RentMapper mapper = sqlSession.getMapper(RentMapper.class);
        Rent rent = mapper.getById("CZ_20190611_094617_32192683");
        System.out.println(rent.getRentid());
        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd");
        System.out.println(simpleDateFormat.format(rent.getBegindate()));
        System.out.println(simpleDateFormat.format(rent.getReturndate()));
        System.out.println(rent.getCarnumber());
        System.out.println(rent.getPrice());
        System.out.println(simpleDateFormat.format(rent.getCreatetime()));
        System.out.println(rent.getCustomer());
    }
}
```

