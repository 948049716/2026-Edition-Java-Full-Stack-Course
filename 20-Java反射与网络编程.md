# 反射的应用

## 反射调用方法

```java
package test;

public class Student {
    private int id;
    private String name;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void show(){
        System.out.println("学生信息");
        System.out.println("ID:" + this.id);
        System.out.println("姓名:" + this.name);
    }

    public int test(int num,int num2){
        return num+num2;
    }
}

```

```java
package test;

import java.lang.reflect.Method;

public class Test {
    public static void main(String[] args) throws Exception {
        Student student = new Student();
        student.setId(1);
        student.setName("张三");
        //常规调用
        student.show();
        //反射调用
        Class clazz = Student.class;
        Method show = clazz.getMethod("show", null);
        show.invoke(student, null);
        Method test = clazz.getMethod("test", int.class,int.class);
        Object invoke = test.invoke(student, 1,2);
        System.out.println(invoke);
    }
}
```

## 反射访问成员变量

```java
package test;

import java.lang.reflect.Field;
import java.lang.reflect.Method;

public class Test {
    public static void main(String[] args) throws Exception {
        Class clazz = Student.class;
        Field[] declaredFields = clazz.getDeclaredFields();
        for (Field declaredField : declaredFields) {
            int modifiers = declaredField.getModifiers();
            Class<?> fieldType = declaredField.getType();
            String fieldName = declaredField.getName();
            System.out.println("成员变量" + fieldName + "的数据类型是：" + fieldType.getName() + ",访问权限：" + getModifiers(modifiers));
        }
    }

    public static String getModifiers(int modifiers){
        String result = null;
        switch (modifiers){
            case 0:
                result = "";
                break;
            case 1:
                result = "public";
                break;
            case 2:
                result = "private";
                break;
            case 3:
                result = "protected";
                break;
        }
        return result;
    }
}
```

## 反射调用构造器

```java
package test;

import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.Method;

public class Test {
    public static void main(String[] args) throws Exception {
        Class clazz = Student.class;
        Constructor<Student> constructor = clazz.getConstructor(null);
        Student student = constructor.newInstance(null);
        Method setId = clazz.getDeclaredMethod("setId", int.class);
        setId.invoke(student, 1);
        Method getId = clazz.getDeclaredMethod("getId", null);
        Object invoke = getId.invoke(student, null);
        System.out.println(invoke);
    }
}
```

# 网络编程

如何使用 Java 开发基于 Web 的应用

## IP 和端口

IP：互联网中的每台终端设备都有一个唯一标识，网络中的请求可以根据这个标识找到具体的终端，这个唯一标识就是 IP。

端口：localhost:3306

## TCP 协议

TCP 面向连接的运输层协议，比较复杂，优点安全，缺点效率低，使用 TCP 协议前必须先建立连接，才能传输数据，数据传输完毕之后需要释放连接。

ServerScoket

| 方法                                                         | 描述                               |
| ------------------------------------------------------------ | ---------------------------------- |
| public ServerSocket(int port)                                | 根据端口创建 ServerSocket 对象     |
| public ServerSocket(int port,int backlog,InetAddress address) | 根据端口、backlog、IP 地址创建对象 |
| public Socket accept()                                       | 等待客户端请求，返回 Socket 对象   |
| public void close()                                          | 关闭 ServerSocket                  |

Socket

| 方法                                     | 描述                                   |
| ---------------------------------------- | -------------------------------------- |
| public Socket(String host,int port)      | 根据主机、端口创建要连接的 Socket 对象 |
| public Socket(InetAddress host,int port) | 根据 IP、端口创建要连接的 Socket 对象  |
| public InputStream getInputStream()      | 获取 Socket 输入流                     |
| public synchronized void close()         | 关闭 Socket                            |

```java
package test;

import java.io.DataInputStream;
import java.io.DataOutputStream;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.ServerSocket;
import java.net.Socket;

public class Test {
    public static void main(String[] args) throws Exception {
        ServerSocket serverSocket = null;
        Socket socket = null;
        OutputStream outputStream = null;
        InputStream inputStream = null;
        DataInputStream dataInputStream = null;
        DataOutputStream dataOutputStream = null;
        serverSocket = new ServerSocket(8080);
        System.out.println("------服务端------");
        System.out.println("已启动，等待接收客户端请求...");
        socket = serverSocket.accept();
        inputStream = socket.getInputStream();
        dataInputStream = new DataInputStream(inputStream);
        String request = dataInputStream.readUTF();
        System.out.println("接收到了客户端请求：" + request);
        String response = "Hello World";
        outputStream = socket.getOutputStream();
        dataOutputStream = new DataOutputStream(outputStream);
        dataOutputStream.writeUTF(response);
        System.out.println("给客户端做出响应：" + response);
        inputStream.close();
        dataInputStream.close();
        outputStream.close();
        dataOutputStream.close();
        socket.close();
        serverSocket.close();
    }
}
```

```java
package test;

import java.io.DataInputStream;
import java.io.DataOutputStream;
import java.io.InputStream;
import java.io.OutputStream;
import java.net.Socket;

public class Client {
    public static void main(String[] args) throws Exception {
        Socket socket = null;
        OutputStream outputStream = null;
        InputStream inputStream = null;
        DataOutputStream dataOutputStream = null;
        DataInputStream dataInputStream = null;
        socket = new Socket("127.0.0.1", 8080);
        System.out.println("------客户端------");
        //给服务器发消息
        String request = "你好";
        System.out.println("客户端说：" + request);
        outputStream = socket.getOutputStream();
        dataOutputStream = new DataOutputStream(outputStream);
        dataOutputStream.writeUTF(request);
        inputStream = socket.getInputStream();
        dataInputStream = new DataInputStream(inputStream);
        String response = dataInputStream.readUTF();
        System.out.println("服务器响应：" + response);
        inputStream.close();
        dataInputStream.close();
        outputStream.close();
        dataOutputStream.close();
        socket.close();
    }
}
```

## UDP 协议

TCP 优点是稳定安全，缺点是效率低，UDP 恰好相反，优点是效率高，缺点是不安全

DatagramSocket

| 方法                                          | 描述                             |
| --------------------------------------------- | -------------------------------- |
| public DatagramSocket(int port)               | 根据端口创建 DatagramSocket 对象 |
| public void send(DatagramPacket p)            | 发送数据包                       |
| public synchronized receive(DatagramPacket p) | 接收数据包                       |

DatagramPacket

| 方法                                                         | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| public DatagramPacket(byte buf[],int length,InetAddress address,int port) | 根据发送的数据、数据长度、IP 地址、端口创建 DatagramPacket 对象 |
| public synchronized byte[] getData()                         | 获取接收的数据                                               |
| public synchronized int getLength()                          | 获取数据长度                                                 |
| public synchronized int getPort()                            | 获取发送数据的 Socket 端口                                   |

```java
package test;

import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.SocketAddress;

public class TerminalA {
    public static void main(String[] args) throws Exception {
        byte[] buff = new byte[1024];
        DatagramPacket datagramPacket = new DatagramPacket(buff, buff.length);
        DatagramSocket datagramSocket = new DatagramSocket(8181);
        datagramSocket.receive(datagramPacket);
        String message = new String(datagramPacket.getData(),0,datagramPacket.getLength());
        System.out.println("我是TerminalA，接收到了" + datagramPacket.getPort() + "传来的数据：" + message);
        String reply = "我是TerminalA，已接收到你发来的数据";
        SocketAddress socketAddress = datagramPacket.getSocketAddress();
        DatagramPacket datagramPacket1 = new DatagramPacket(reply.getBytes(), reply.getBytes().length,socketAddress);
        datagramSocket.send(datagramPacket1);
    }
}
```

```java
package test;

import java.net.DatagramPacket;
import java.net.DatagramSocket;
import java.net.InetAddress;
import java.net.SocketAddress;

public class TerminalB {
    public static void main(String[] args) throws Exception {
        String message = "我是TerminalB，你好！";
        InetAddress inetAddress = InetAddress.getByName("localhost");
        DatagramPacket datagramPacket = new DatagramPacket(message.getBytes(), message.getBytes().length,inetAddress,8181);
        DatagramSocket datagramSocket = new DatagramSocket(8080);
        datagramSocket.send(datagramPacket);
        byte[] buff = new byte[1024];
        DatagramPacket datagramPacket1 = new DatagramPacket(buff, buff.length);
        datagramSocket.receive(datagramPacket1);
        String reply = new String(datagramPacket1.getData(),0,datagramPacket1.getLength());
        System.out.println("我是TerminalB，接收到了" + datagramPacket1.getPort() + "返回的数据：" + reply);
    }
}
```

## 多线程下的网络编程

```java
package test;

import java.net.ServerSocket;
import java.net.Socket;

public class ServerThread {
    public static void main(String[] args) throws Exception {
        ServerSocket serverSocket = new ServerSocket(8080);
        System.out.println("服务器已启动...");
        while (true) {
            Socket socket = serverSocket.accept();
            new Thread(new ServerRunnable(socket)).start();
        }
    }
}
```

```java
package test;

import java.io.DataInputStream;
import java.io.IOException;
import java.io.InputStream;
import java.net.Socket;

public class ServerRunnable implements Runnable {
    private Socket socket;

    public ServerRunnable(Socket socket) {
        this.socket = socket;
    }

    @Override
    public void run() {
        InputStream inputStream = null;
        DataInputStream dataInputStream = null;
        try {
            inputStream = this.socket.getInputStream();
            dataInputStream = new DataInputStream(inputStream);
            String message = dataInputStream.readUTF();
            System.out.println(message);
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                inputStream.close();
                dataInputStream.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

```java
package test;

public class ClientThread {
    public static void main(String[] args) {
        for (int i = 0; i < 100; i++) {
            new Thread(new ClientRunnable(i)).start();
        }
    }
}
```

```java
package test;

import java.io.DataOutputStream;
import java.io.IOException;
import java.io.OutputStream;
import java.net.Socket;

public class ClientRunnable implements Runnable {

    private int num;

    public ClientRunnable(int num) {
        this.num = num;
    }

    @Override
    public void run() {
        Socket socket = null;
        OutputStream outputStream = null;
        DataOutputStream dataOutputStream = null;
        try {
            socket = new Socket("localhost", 8080);
            String message = "我是客户端"+this.num;
            outputStream = socket.getOutputStream();
            dataOutputStream = new DataOutputStream(outputStream);
            dataOutputStream.writeUTF(message);
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                dataOutputStream.close();
                outputStream.close();
                socket.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

