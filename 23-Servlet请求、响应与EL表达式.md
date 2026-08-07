# Java Web

Servlet 是 Java 进行 Web 开发的基础

Spring MVC 基于 Servlet

Web 接收请求 + 做出响应

GET 请求进入到 doGet 方法，POST 请求进入到 doPost 方法

HttpServletRequest 表示请求

HttpServletResponse 表示响应

面向对象

## HttpServletRequest

表示请求

接收客户端的参数 getParameter

设置编码 setCharacterEncoding

url 和 uri

url 完整的请求路径 http://localhost:8080/test.html

uri 具体资源名称 test.html

获取 Session getSession

session 和 request 一样，都 Java Web 提供的对象

session 用来存储登录用户信息

JSP：Java Server Page HTML 代码和 Java 代码的混合体

JSP 内置对象，JSP 引擎预先为开发者创建好的 Java 对象，可以直接使用

url 中的参数只能通过 getParameter 方法取出

getAttribute 适用于两个资源之间的数据传输，必须先有 setAttribute 才能通过 getAttribute 进行取值

资源之间的跳转有两种形式：

- 转发 getRequestDispatcher
- 重定向 sendRedirect

转发和重定向的区别：

1、转发操作 request，重定向操作 response

2、转发浏览器地址栏不变，重定向浏览器地址栏会改变

**3、转发前后是同一个 request 对象，重定向前后是两个 request 对象 **

如果通过 request  进行数据的传递，则必须使用转发，如果使用重定向，会造成数据无法取出的问题。

如果使用重定向的方式进行资源跳转，同时需要数据共享，则此时需要使用 session 进行数据存储。

session 作用域大于 request

业务数据查询，后台查出数据，转发到页面进行展示，使用 request 存储数据

登录用户，多个页面多个场景中使用，session 存储数据

## EL 表达式

EL 就是用来简化 JSP 页面取值的，简化 request.getAttribute

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <form action="/login" method="post">
        <table>
            <tr>
                <td>用户名：</td>
                <td>
                    <input type="text" name="username"/>
                    <font style="color: red">${msg1}</font>
                </td>
            </tr>
            <tr>
                <td>密码：</td>
                <td>
                    <input type="password" name="password"/>
                    <font style="color: red">${msg2}</font>
                </td>
            </tr>
            <tr>
                <td>
                    <input type="submit" value="登录"/>
                </td>
            </tr>
        </table>
    </form>
</body>
</html>
```

```java
package test;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.io.IOException;

@WebServlet("/login")
public class LoginServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String username = (String) req.getParameter("username");
        String password = (String) req.getParameter("password");
        if(!username.equals(DBUtils.USERNAME)){
            //给页面提示用户名错误
            req.setAttribute("msg1", ResponseEnum.USERNAME_ERROR.getMsg());
            req.getRequestDispatcher("login.jsp").forward(req, resp);
        }
        if(!password.equals(DBUtils.PASSWORD)){
            req.setAttribute("msg2", ResponseEnum.PASSWORD_ERROR.getMsg());
            req.getRequestDispatcher("login.jsp").forward(req, resp);
        }
        //登录成功
        HttpSession session = req.getSession();
        session.setAttribute("username", username);
        //跳转到首页
        resp.sendRedirect("index.jsp");
    }
}
```

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    欢迎回来，${username}<a href="/logout">退出</a>
    <h1>首页</h1>
</body>
</html>
```

```java
package test;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;
import java.io.IOException;

@WebServlet("/logout")
public class LogoutServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        HttpSession session = req.getSession();
        session.invalidate();
        resp.sendRedirect("login.jsp");
    }
}
```

## HttpServletResponse

向客户端返回资源数据 getWriter().write();

Servlet 和 JSP 的关系：

JSP 本质就是一个 Servlet，为了方便编写 HTML 代码，如果用 Servlet 返回 HTML 代码，比较麻烦，所有创造了 JSP 机制，是一个模板，在此模板上可以用开发 HTML 代码的形式写程序，JSP 自动转换成一个 Servlet，以 response 的形式将数据返回给客户端。

