# 数据库

Web：前端 -》后端 -》数据库

前端：负责用户交互

后端：业务逻辑

数据库：持久化

关系型数据库和非关系型数据库

关系型数据库 MySQL

## 数据库管理工具

数据库是安装在电脑上的一种服务 DataBase

SQLyog、Navicate、DataGrip

MySQL 8

SQL 是一种编程语言，专门用来管理数据库

1、DML：数据操作语言，操作数据库中包含的数据（insert、update、delete）

2、DDL：数据定义语言，创建、删除、修改数据库、数据表

数据库 -》数据表 -》数据

3、DQL：数据查询语言，对数据库中的数据进行查询

4、DCL：数据控制语言，用来控制数据库组件的存取（事务）

学习数据库，从两方面入手：

1、掌握数据库的基本使用

2、设计数据库：根据项目需求，设计表跟表之间的关系

## 数据库的使用

1、创建数据库实例

```sql
create database mytest1 default character set utf8 collate utf8_general_ci;
```

utf8_general_ci：排序数据不区分大小写

utf8_bin：排序数据区分大小写

B：66

a：97

2、删除数据库实例

```sql
drop database mytest2;
```

3、创建数据表

```sql
create table 表名(
	字段名称 数据类型,
	字段名称 数据类型,
	......
);
```

整数类型：tinyint、smallint、mediumint、int、bigint

浮点类型：float、double

日期类型：date、datetime、timestamp

字符串类型：char、varchar、text

二进制类型：bit、binary、varbinary、tinyblob、blob、mediumblob、longblob

4、删除数据库

```sql
drop table user;
```

5、修改表结构

- 新增一个字段 

  ```sql
  alter table student add sex varchar(2)
  ```

- 修改一个字段

  ```sql
  alter table student change sex gender int;
  ```

- 删除一个字段

  ```sql
  alter table student drop gender;
  ```

## SQL 函数

SQL 语句的下标从 1 开始

> 数学函数

```sql
select abs(-20);   //求绝对值
select floor(19.9); //求小于参数的最大整数
select ceil(19.0001); //求大于参数的最小整数
```

> 字符串函数

```sql
select insert(s1,index,len,s2); //s1中index位置开始，长度为len的字符替换为s2  
select upper("java");          //转大写
select lower("JAVA");		   //转小写
select left(name,2);		   //从左边开始截取name的前2个字符
select right(name,2);		   //从右边开始截取name的前2个字符
select substring(name,2,3);    //从name的第2个字符开始截取长度为3的字符串
select reverse(name);		   //反序输出name
```

> 日期函数

```sql
select curdate();           //获取当前日期
select curtime();           //获取当前时间
select now();               //获取当前日期 + 时间
select datediff('2023-06-11','2018-06-23'); //计算两个日期之间相隔的天数
select adddate('2023-06-11',200);      //计算当前日期200天之后的日期
select subdate('2023-06-11',200);      //计算当前日期200天之前的日期
```

> 聚合函数

```sql
select count(*) from student;      //统计student表中有多少条数据
select sum(score) from student;		//计算score的和
select avg(score) from student;		//求平均值
select max(score) from student;		//求最大值
select min(score) from student;		//求最小值
```

> 分组

```sql
select count(*),opername from bus_rent group by opername;       //根据operanme进行分组统计
select count(*),opername from bus_rent group by opername order by count(*) asc; //升序排列
select count(*),opername from bus_rent group by opername order by count(*) desc; //降序排列
select count(*),opername from bus_rent group by opername having count(*) > 1; //条件筛选大于1的数据
```

## MySQL 运算符

> 算术运算符

```sql
select score / 100 from student;  							//执行运算符：加减乘除
select score < 100 from student;					//比较运算符：大于、小于、等于、不等于
select score < 100 && id > 1 from student where id = 2;          //逻辑运算符
select score < 100 || id > 1 from student where id = 2;          //逻辑运算符
select !(score < 100) || !(id > 1) from student where id = 2;	 //逻辑运算符
```

> 特殊运算符

```sql
select name is null from student;						//判断name是否为null
select score between 70 and 200 from student;			//判断score是否在70-200之间
```