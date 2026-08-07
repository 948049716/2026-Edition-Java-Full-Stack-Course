# MyBatis 延迟加载

延迟加载、惰性加载、懒加载、按需加载，提升程序运行效率的方式

持久层（访问数据库）原则：Java 程序和数据库交互频率越低越好

MyBatis 通过延迟加载来减少 Java 和数据库交互次数

Class 和 Student

查询 Student ，会将对应的 Class 一并查出

延迟加载的思路：查询 Student 的时候，如果没有调用 Class 的相关字段，则只执行一条 SQL 只查 Student，

如果需要访问 Class 的相关字段，此时再执行第二条 SQL

根据具体的需求，动态选择 SQL 语句的条数

MySQL 关系型数据库

Java 面向对象

ORM 框架关联关系型数据库和面向对象编程语言

Object Relationship Mapping：对象关系映射

1、创建实体类

```java
package com.southwind.entity;

import lombok.Data;

import java.util.List;

@Data
public class Class {
    private Integer id;
    private String name;
    private List<Student> students;
}
```

```java
package com.southwind.entity;

import lombok.Data;

@Data
public class Student {
    private Integer id;
    private String name;
    private Class clazz;
}
```

2、创建 Mapper 接口以及 Mapper.xml

```java
package com.southwind.mapper;

import com.southwind.entity.Class;

public interface ClassMapper {
    public Class getById(Integer id);
}
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.southwind.mapper.ClassMapper">

    <select id="getById" parameterType="java.lang.Integer" resultType="com.southwind.entity.Class">
        select * from class where id = #{id}
    </select>

</mapper>
```

```java
package com.southwind.mapper;

import com.southwind.entity.Student;

public interface StudentMapper {
    public Student getById(Integer id);
}
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.southwind.mapper.StudentMapper">

    <resultMap id="studentMap" type="com.southwind.entity.Student">
        <id column="id" property="id"></id>
        <result column="name" property="name"></result>
        <association
            property="clazz"
            javaType="com.southwind.entity.Class"
            column="cid"
            select="com.southwind.mapper.ClassMapper.getById"></association>
    </resultMap>

    <select id="getById" parameterType="java.lang.Integer" resultMap="studentMap">
        select * from student where id = #{id}
    </select>

</mapper>
```

3、config.xml 中开启延迟加载

```xml
<settings>
    <!-- 打印SQL-->
    <setting name="logImpl" value="STDOUT_LOGGING" />
    <!-- 开启延迟加载-->
    <setting name="lazyLoadingEnabled" value="true"/>
</settings>
```

4、Test

```java
package com.southwind;

import com.southwind.entity.News;
import com.southwind.entity.Rent;
import com.southwind.entity.Student;
import com.southwind.mapper.NewsMapper;
import com.southwind.mapper.RentMapper;
import com.southwind.mapper.StudentMapper;
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
        StudentMapper mapper = sqlSession.getMapper(StudentMapper.class);
        Student student = mapper.getById(1);
        System.out.println(student.getName());
    }
}
```

# MyBatis 缓存

延迟加载是在多表关联查询的情况下使用，如果是单表，则延迟加载不起作用，单表的情况下，如何提升程序运行效率？

可以使用缓存，减少 Java 程序和数据库的交互次数，从而提升程序的运行效率。

当查询出数据后，将数据保存到缓存中，当下一次需要使用同样的数据时，直接从缓存中取出数据，不需要再查询数据库

一级缓存默认开启，无法关闭，SqlSession 级别的缓存，同一个 SqlSession 的情况下，一级缓存有效，如果使用两个 SqlSession，则一级缓存失效，一级缓存不需要进行任何配置，直接使用即可。

二级缓存是比一级缓存作用域更大的缓存机制，它是 Mapper 级别的，只要是同一个 Mapper，无论使用多少个 SqlSession，二级缓存都是存在的。

二级缓存默认是关闭的，需要手动开启。

```java
package com.southwind;

import com.southwind.entity.Class;
import com.southwind.entity.News;
import com.southwind.entity.Rent;
import com.southwind.entity.Student;
import com.southwind.mapper.ClassMapper;
import com.southwind.mapper.NewsMapper;
import com.southwind.mapper.RentMapper;
import com.southwind.mapper.StudentMapper;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.InputStream;

public class Test {
    public static void main(String[] args) {
        InputStream resourceAsStream = Test.class.getClassLoader().getResourceAsStream("config.xml");
        SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
        SqlSessionFactory sqlSessionFactory = builder.build(resourceAsStream);
        SqlSession sqlSession = sqlSessionFactory.openSession();
        //获取Mapper的代理对象
        ClassMapper mapper = sqlSession.getMapper(ClassMapper.class);
        Class aClass = mapper.getById(2);
        System.out.println(aClass);
        sqlSession.close();
        sqlSession = sqlSessionFactory.openSession();
        mapper = sqlSession.getMapper(ClassMapper.class);
        Class aClass1 = mapper.getById(2);
        System.out.println(aClass1);
    }
}
```

开启二级缓存

1、config.xml 中配置

```xml
<settings>
    <setting name="cacheEnabled" value="true"/>
</settings>
```

2、Mapper.xml 中配置

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.southwind.mapper.ClassMapper">

    <cache></cache>

    <select id="getById" parameterType="java.lang.Integer" resultType="com.southwind.entity.Class">
        select * from class where id = #{id}
    </select>

</mapper>
```

3、实体类实现序列化接口

```java
package com.southwind.entity;

import lombok.Data;

import java.io.Serializable;
import java.util.List;

@Data
public class Class implements Serializable {
    private Integer id;
    private String name;
    private List<Student> students;
}
```

# MyBatis 动态 SQL

MyBatis 需要开发者手动写 SQL

MyBatis 提供了动态 SQL 的功能，可以根据具体业务需求动态拼接 SQL 语句

1、建表

```sql
create table user(
    id int primary key auto_increment,
    username varchar(11),
    password varchar(11),
    age int
);
```

2、创建实体类

```java
package com.southwind.entity;

import lombok.Data;

@Data
public class User {
    private Integer id;
    private String username;
    private String password;
    private Integer age;
}
```

3、创建 UserMapper

```java
package com.southwind.mapper;

import com.southwind.entity.User;

public interface UserMapper {
    public User get(User user);
}
```

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.southwind.mapper.UserMapper">

   <select id="get" parameterType="com.southwind.entity.User" resultType="com.southwind.entity.User">
        select * from user where id = #{id} and username = #{username} and password = #{password} and age = #{age}
    </select>

</mapper>
```

4、config.xml 注入 MapperXML

```xml
<mappers>
    <mapper resource="com/southwind/mapper/UserMapper.xml"></mapper>
</mappers>
```

修改为动态 SQL

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.southwind.mapper.UserMapper">

   <select id="get" parameterType="com.southwind.entity.User" resultType="com.southwind.entity.User">
        select * from user
        <where>
            <if test="id != null">
                id = #{id}
            </if>
            <if test="username != null">
                and username = #{username}
            </if>
            <if test="password != null">
                and password = #{password}
            </if>
            <if test="age != null">
                and age = #{age}
            </if>
        </where>
    </select>

</mapper>
```