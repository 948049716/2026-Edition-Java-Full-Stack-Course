# 手写 DBUtils 工具

1、定义 QueryRunner 类

2、query 四个参数：Connection、SQL 语句、处理器（将 ResultSet 转换为 Java Bean）、SQL 语句参数

3、参数的非空校验 Connection、SQL、处理器

4、使用 JDBC 的方式进行 SQL 查询，获取结果集 ResultSet

5、调用处理器的 handle 方法将 ResultSet 解析为对应的 Java Bean

6、返回封装好的 Java Bean

```java
package db;

import org.apache.commons.dbutils.ResultSetHandler;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

public class MyQueryRunner {

    public <T> T query(Connection connection, String sql, ResultSetHandler<T> rsh,Object... params){
        if(connection == null){
            throw new MyException("异常：Connection为空");
        }
        if(sql == null){
            throw new MyException("异常：SQL为空");
        }
        if(rsh == null){
            throw new MyException("异常：ResultSetHandler为空");
        }
        Object result = null;
        try {
            //生成Statement对象
            PreparedStatement statement = connection.prepareStatement(sql);
            //替换参数
            if (params != null) {
                int length = params.length;
                for (int i = 0; i < length; i++) {
                    if(params[i] instanceof Integer){
                        statement.setInt(i+1, (int)params[i]);
                    }
                    if(params[i] instanceof String) {
                        statement.setString(i+1, (String) params[i]);
                    }
                }
            }
            //执行SQL
            ResultSet resultSet = statement.executeQuery();
            //将ResultSet转换为JavaBean
            result = rsh.handle(resultSet);
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        }
        return (T) result;
    }

}
```

```java
package db;

import org.apache.commons.dbutils.ResultSetHandler;

import java.lang.reflect.Field;
import java.lang.reflect.Method;
import java.sql.ResultSet;
import java.sql.ResultSetMetaData;
import java.sql.SQLException;

public class MyBeanHandler<T> implements ResultSetHandler<T> {
    private final Class<? extends T> type;

    public MyBeanHandler(Class<? extends T> type) {
        this.type = type;
    }

    @Override
    public T handle(ResultSet resultSet) throws SQLException {
        T t = null;
        try {
            t = type.newInstance();
            //将ResultSet的数据填充到t中
            //获取实体类的属性
            //ResultSet的字段
            Field[] declaredFields = type.getDeclaredFields();
            if (resultSet.next()) {
                for (Field declaredField : declaredFields) {
                    ResultSetMetaData metaData = resultSet.getMetaData();
                    int columnCount = metaData.getColumnCount();
                        for (int i = 1; i <= columnCount; i++) {
                            if (declaredField.getName().equals(metaData.getColumnName(i))) {
                                //匹配后进行赋值
                                //rs的字段赋值给JavaBean的属性
                                Object value = null;
                                switch (metaData.getColumnTypeName(i)){
                                    case "INT":
                                        value = resultSet.getInt(i);
                                        break;
                                    case "VARCHAR":
                                        value = resultSet.getString(i);
                                        break;
                                }
                                //set方法
                                String methodName = "set" + declaredField.getName().substring(0, 1).toUpperCase()+ declaredField.getName().substring(1);
                                try {
                                    Method declaredMethod = type.getDeclaredMethod(methodName, declaredField.getType());
                                    declaredMethod.invoke(t,value);
                                } catch (Exception e) {
                                    e.printStackTrace();
                                }

                            }
                        }
                    }
            } else {
                return null;
            }
        } catch (InstantiationException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        }
        return t;
    }
}
```

```java
package db;

import org.apache.commons.dbutils.ResultSetHandler;

import java.lang.reflect.Field;
import java.lang.reflect.Method;
import java.sql.ResultSet;
import java.sql.ResultSetMetaData;
import java.sql.SQLException;
import java.util.ArrayList;
import java.util.List;

public class MyBeanListHandler<T> implements ResultSetHandler<List<T>> {
    private final Class<? extends T> type;

    public MyBeanListHandler(Class<? extends T> type) {
        this.type = type;
    }

    @Override
    public List<T> handle(ResultSet resultSet) throws SQLException {
        T t = null;
        List<T> list = new ArrayList<>();
        try {
            Field[] declaredFields = type.getDeclaredFields();
            while (resultSet.next()){
                t = type.newInstance();
                list.add(t);
                for (Field declaredField : declaredFields) {
                    ResultSetMetaData metaData = resultSet.getMetaData();
                    int columnCount = metaData.getColumnCount();
                    for (int i = 1; i <= columnCount; i++) {
                        if (declaredField.getName().equals(metaData.getColumnName(i))) {
                            //匹配后进行赋值
                            //rs的字段赋值给JavaBean的属性
                            Object value = null;
                            switch (metaData.getColumnTypeName(i)){
                                case "INT":
                                    value = resultSet.getInt(i);
                                    break;
                                case "VARCHAR":
                                    value = resultSet.getString(i);
                                    break;
                            }
                            //set方法
                            String methodName = "set" + declaredField.getName().substring(0, 1).toUpperCase()+ declaredField.getName().substring(1);
                            try {
                                Method declaredMethod = type.getDeclaredMethod(methodName, declaredField.getType());
                                declaredMethod.invoke(t,value);
                            } catch (Exception e) {
                                e.printStackTrace();
                            }

                        }
                    }
                }
            }
        } catch (InstantiationException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        }
        return list;
    }
}
```

```java
package db;

import java.sql.Connection;
import java.sql.DriverManager;
import java.util.List;

public class Test {
    public static void main(String[] args) throws Exception {
        String url = "jdbc:mysql://localhost:3306/mytest1";
        String user = "root";
        String pwd = "root";
        Connection connection = DriverManager.getConnection(url, user, pwd);
//        String sql = "select * from person where id = ?";
//        MyQueryRunner queryRunner = new MyQueryRunner();
//        Person person = queryRunner.query(connection, sql, new MyBeanHandler<>(Person.class), 2);
//        System.out.println(person);
        String sql = "select * from person";
        MyQueryRunner queryRunner = new MyQueryRunner();
        List<Person> list = queryRunner.query(connection, sql, new MyBeanListHandler<>(Person.class), null);
        for (Person person : list) {
            System.out.println(person);
        }
    }
}
```

