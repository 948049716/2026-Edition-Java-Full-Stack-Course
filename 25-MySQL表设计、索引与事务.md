# MySQL运算符

in 判断值是否在某个特定区间内

```sql
select * from student where id in (1,2,3);
```

like 

```sql
select * from student where name like '%三%'
```

# 表设计

主键：表中的一个字段，该字段的值是每一行数据的唯一标识

一般会将主键设置为 id，int 类型，自增，主键的值不能重复

```sql
create table user(
    id int primary key auto_increment,
    name varchar(20)
);
```

外键：表中的一个字段，与其他表的主键建立约束关系，主键的值约束外键的值

外键是为了保证业务逻辑的准确性，但是效率较低，一般项目开发中不使用外键

数据表之间的三种关系

- 一对一：A表的一条数据只能对应B表的一条数据，B表的一条数据只能对应A表的一条数据
- 一对多：A表的一条数据只能对应B表的一条数据，B表的一条数据可以对应A表的多条数据
- 多对多：A表的一条数据可以对应B表的多条数据，B表的一条数据可以对应A表的多条数据

```sql
select a.area,b.number from area a,bike b where a.id = b.aid and a.id = 99;
```

```sql
select s.name student_name,c.name course_name from student s,course c,course_student cs where s.id = cs.sid and c.id = cs.cid and s.id = 1
```

# 索引

索引是一种特殊的数据库结构，可以用来快速查询数据表中的特定记录，索引是提高数据库性能的重要方式，所有的字段都可以添加索引。

索引包括：普通索引、唯一性索引、全文索引、单列索引、多列索引、空间索引。

使用索引可以提升查询速度，但是创建和维护索引需要耗费时间，索引需要占用物理空间。

普通索引：不需要任何限制条件的索引，可以在任意数据类型创建

唯一索引：索引的值必须唯一，比如主键索引

全文索引：只能创建在 char varchar text 类型的字段上，查询数据量较大的字符串类型字段时，使用全文索引可以提高速度。

单列索引：只能对应一个字段的索引

多列索引：在一张表的多个字段上创建一个索引，多个字段共同组成一个索引

空间索引：只能建立在空间数据库上（GIS）

索引用来做快速排序的，主键自带索引，其他字段可以手动添加索引

索引的设计原则：

- 出现在 where 语句中的列，不是 select 后面要查询的列
- 索引的值，尽量唯一，效率更高
- 不要添加过多的索引，维护成本很高

添加索引

```sql
alter table student add index in_name(name);
```

删除索引

```sql
alter table student drop index in_name;
```

# 事务

将多条 SQL 作为一个整体，要么全部执行，要么一条都不执行

四个特性：

- 原子性：多条 SQL 是一个整体，不可再分割
- 一致性：SQL 执行前后，数据的值保持一致
- 隔离性：一个事务的执行不能被其他事务干扰
- 持久性：一个事务一旦提交，数据的改变是永久性的

# 视图

数据库中一张虚拟的表，允许不同用户或应用程序以不同的方式查看同一张表中的数据

```sql
create view view_common as select id, name, card_id from person;
create view view_all as select * from person;
```

使用视图

```sql
select * from view_common;
select * from view_all;
```

删除视图

```sql
drop view view_common;
drop view view_all;
```

# 触发器

触发器定义了一系列的操作，可以在对指定表进行插入、更新、删除操作的同时自动执行这些操作

1、开发更快，因为触发器存储在数据库中，所以不需要在应用程序中重复编写触发器代码

2、更容易维护，定义触发器后，访问目标表，会自动调用触发器

3、如果修改业务，只需要修改触发器即可，不需要修改业务代码

创建触发器

```sql
create trigger t_afterinsert_on_tab1
    after insert on tab1
    for each row
    begin
        insert into tab2(tab2_id) values(new.tab1_id);
    end;
```

```sql
create trigger t_afterdelete_on_tab1
    after delete on tab1
    for each row
    begin
        delete from tab2 where tab2_id = old.tab1_id;
    end;
```

删除触发器

```sql
drop trigger t_afterinsert_on_tab1;
drop trigger t_afterdelete_on_tab1;
```

# 存储过程

存储过程是一组为了完成特定功能的 SQL 语句的集合，经过编译存储在数据库中，用户通过指定存储过程的名称并给出参数来执行。

优点：

- 模块化程序设计，只需要创建一次存储过程，以后就可以在程序中任意调用
- 执行速度更快，同样的 SQL 重复执行，使用存储过程更快
- 更换的安全机制，对于没有权限执行存储过程的用户，可以通过授权的方式执行

创建存储过程

```sql
create procedure add_name(in target int)
begin
    declare name varchar(11);
    if target = 1 then
        set name = 'MySQL';
        else
        set name = 'Java';
    end if;
    insert into course(name) values(name);
end;
```

调用存储过程

```sql
call add_name(1);
```

删除存储过程

```sql
drop procedure add_name;
```

创建存储过程

```sql
create procedure count_of_student(out count_num int)
begin
    select count(*) from student;
end;
```

调用存储过程

```sql
call count_of_student(@count_num);
```

删除存储过程

```sql
drop procedure count_of_student;
```