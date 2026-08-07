# Java Web

Tomcat 配置

## 前端

HTML + CSS + JS

HTML：组成网页的基础标签

CSS：对网页进行美化

JS：动态操作数据，JS 的封装库，jQuery 

### HTML

编写网页最基础的组件标签

div、form、input、select、radio、img、table

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8" />
		<title></title>
		<link rel="stylesheet" href="css/index.css">
		<script src="js/jquery-3.3.1.min.js"></script>
		<script src="js/index.js"></script>
	</head>
	<body>
		<div>123</div>
	</body>
</html>
```

```css
div{
	width: 300px;
	height: 300px;
	border: 10px solid blue;
}
```

```js
$(function(){
	$("div").click(function(){
		alert('点击了div')
	})
})
```



```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8" />
		<title></title>
		<link rel="stylesheet" href="css/index.css">
		<script src="js/jquery-3.3.1.min.js"></script>
		<script src="js/index.js"></script>
	</head>
	<body>
		<div id="main">
			<form action="/register" method="post">
				<table id="tab">
					<tr>
						<td class="title">用户名：</td>
						<td>
							<input type="text" />
						</td>
					</tr>
					<tr>
						<td class="title">密码：</td>
						<td>
							<input type="password" />
						</td>
					</tr>
					<tr>
						<td class="title">确认密码：</td>
						<td>
							<input type="password" />
						</td>
					</tr>
					<tr>
						<td class="title">性别：</td>
						<td>
							<input type="radio" name="sex" checked="checked"/>男
							<input type="radio" name="sex"/>女
						</td>
					</tr>
					<tr>
						<td class="title">科目：</td>
						<td>
							<select>
								<option>Java</option>
								<option>C++</option>
								<option>Python</option>
							</select>
						</td>
					</tr>
					<tr>
						<td class="title">爱好：</td>
						<td>
							<input type="checkbox" name="hobby"/>编程
							<input type="checkbox" name="hobby"/>游泳
							<input type="checkbox" name="hobby"/>唱歌
							<input type="checkbox" name="hobby"/>画画
						</td>
					</tr>
				</table>
				<input id="btn" type="submit" value="提交"></input>
			</form>
		</div>
	</body>
</html>
```

前端 UI 组件

LayUI、Element UI、ECharts、BaiDuMap

## 后端

Java Web 提供的后端组件 Servlet

Servlet 是一个接口，让 Java 具备接收网络请求，并且做出响应的功能

```java
package test;

import javax.servlet.*;
import javax.servlet.annotation.WebServlet;
import java.io.IOException;

@WebServlet("/test")
public class MyServlet implements Servlet {
    @Override
    public void init(ServletConfig servletConfig) throws ServletException {

    }

    @Override
    public ServletConfig getServletConfig() {
        return null;
    }

    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        String idStr = servletRequest.getParameter("id");
        System.out.println("接收到了客户端参数：" + idStr);
        servletResponse.getWriter().write("Hello World");
    }

    @Override
    public String getServletInfo() {
        return null;
    }

    @Override
    public void destroy() {

    }
}
```

```java
package test;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet("/test")
public class TestServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("调用了Get方法");
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("调用了Post方法");
    }
}
```

前端：

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8" />
    <title></title>
    <link rel="stylesheet" href="/css/index.css">
</head>
<body>
<div id="main">
    <form action="/test" method="post">
        <table id="tab">
            <tr>
                <td class="title">用户名：</td>
                <td>
                    <input name="username" type="text" />
                </td>
            </tr>
            <tr>
                <td class="title">密码：</td>
                <td>
                    <input name="password" type="password" />
                </td>
            </tr>
            <tr>
                <td class="title">确认密码：</td>
                <td>
                    <input name="repassword" type="password" />
                </td>
            </tr>
            <tr>
                <td class="title">性别：</td>
                <td>
                    <input type="radio" value="男" name="sex" checked="checked"/>男
                    <input type="radio" value="女" name="sex"/>女
                </td>
            </tr>
            <tr>
                <td class="title">科目：</td>
                <td>
                    <select name="type">
                        <option>Java</option>
                        <option>C++</option>
                        <option>Python</option>
                    </select>
                </td>
            </tr>
            <tr>
                <td class="title">爱好：</td>
                <td>
                    <input type="checkbox" value="编程" name="hobby"/>编程
                    <input type="checkbox" value="游泳" name="hobby"/>游泳
                    <input type="checkbox" value="唱歌" name="hobby"/>唱歌
                    <input type="checkbox" value="画画" name="hobby"/>画画
                </td>
            </tr>
        </table>
        <input id="btn" type="submit" value="提交"></input>
    </form>
</div>
</body>
</html>
```

后端：

```java
package test;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet("/test")
public class TestServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("调用了Get方法");
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        req.setCharacterEncoding("UTF-8");
        String username = req.getParameter("username");
        String password = req.getParameter("password");
        String repassword = req.getParameter("repassword");
        String sex = req.getParameter("sex");
        String type = req.getParameter("type");
        String hobby = req.getParameter("hobby");
        System.out.println(username);
        System.out.println(password);
        System.out.println(repassword);
        System.out.println(sex);
        System.out.println(type);
        System.out.println(hobby);
    }
}
```