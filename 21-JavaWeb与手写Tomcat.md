# Java Web

使用 Java 语言开发 Web 应用，Java 最主流的研发方向

Servlet、JSP

Tomcat：Web 应用服务器，专门用来运行 Web 程序的

bin：启动/关闭 Tomcat 服务的命令

conf：配置文件

lib：jar 包

logs：日志

temp：临时文件

webapps：存放程序

work：JSP 文件转换之后的 Servlet 文件

启动一个服务，端口是 8080，浏览器可以通过 url 可以访问服务，返回该服务中的资源

## 手写简易版 Tomcat

Socket 

Request

Response

响应：根据浏览器所请求的 uri 去找对应的资源，如果该资源则直接返回，否则返回 404 错误

```java
package test;

import java.io.File;
import java.io.IOException;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.InetAddress;
import java.net.ServerSocket;
import java.net.Socket;

public class MyHttpServer {
    private int port = 8080;
    public static String WebContent = System.getProperty("user.dir")+ File.separator +"WebContent";

    public void receiving(){
        ServerSocket serverSocket = null;
        try {
            serverSocket = new ServerSocket(port, 1, InetAddress.getByName("127.0.0.1"));
        } catch (IOException e) {
            e.printStackTrace();
        }
        //接收请求
        while (true){
            Socket socket = null;
            InputStream inputStream = null;
            OutputStream outputStream = null;
            try {
                socket = serverSocket.accept();
                inputStream = socket.getInputStream();
                outputStream = socket.getOutputStream();
                //解析请求
                MyHttpRequest request = new MyHttpRequest(inputStream);
                request.parse();
                //做出响应
                MyHttpResponse response = new MyHttpResponse(outputStream);
                response.response(request);
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }

}
```

```java
package test;

import java.io.IOException;
import java.io.InputStream;

public class MyHttpRequest {
    private InputStream inputStream;
    private String uri;

    public MyHttpRequest(InputStream inputStream) {
        this.inputStream = inputStream;
    }

    public void parse(){
        StringBuffer stringBuffer = new StringBuffer();
        int i = 0;
        byte[] buffer = new byte[2048];
        try {
            i = inputStream.read(buffer);
        } catch (IOException e) {
            e.printStackTrace();
        }
        for (int j = 0; j < i; j++) {
            stringBuffer.append((char)buffer[j]);
        }
        uri = parseUri(stringBuffer.toString());
    }

    public String parseUri(String stringBuffer){
        int index1,index2;
        index1 = stringBuffer.indexOf(' ');
        index2 = stringBuffer.indexOf(' ',index1 + 1);
        return stringBuffer.substring(index1+1,index2);
    }

    public String getUri() {
        return uri;
    }
}
```

```java
package test;

import java.io.File;
import java.io.FileInputStream;
import java.io.OutputStream;

public class MyHttpResponse {
    private OutputStream outputStream;

    public MyHttpResponse(OutputStream outputStream) {
        this.outputStream = outputStream;
    }

    public void response(MyHttpRequest request) throws Exception{
        byte[] bytes = new byte[1024];
        FileInputStream fileInputStream = null;
        String filePath = request.getUri();
        if(filePath.equals("/")){
            filePath = "/index.html";
        }

        String result = null;
        File file = new File(MyHttpServer.WebContent,filePath);
        byte[] fileByte = new byte[(int)file.length()];
        if(file.exists()){
            fileInputStream = new FileInputStream(file);
            fileInputStream.read(fileByte);
            fileInputStream.close();
            result = new String(fileByte);
            result = createMsg("200", result);
            outputStream.write(result.getBytes());
        } else {
            String error = "404 File Not Found!";
            String msg = createMsg("404", error);
            outputStream.write(msg.getBytes());
        }
    }

    public String createMsg(String code,String message){
        return "HTTP/1.1 " + code + "\r\n" + "Content-Length: " + message.length()
                + "\r\n" + "\r\n" + message;
    }
}
```

```java
package test;

public class Test {
    public static void main(String[] args) {
        System.out.println("Server startup successfully");
        MyHttpServer server = new MyHttpServer();
        server.receiving();
    }
}
```