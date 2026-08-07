# 异常

异常是 Java 处理错误的一种机制，为程序员指出程序中存在的问题，程序员通过异常信息发现问题，分析问题，从而解决问题。

异常不是编译时错误，不是语法错误，是运行时错误，程序语法正确，能够正常通过编译，但是在运行期间报错。

会将具体的错误信息以及出错位置统一告知程序员

## 异常的使用

try-catch

try：监听可能会抛出异常的代码，一旦出现错误，JDK 会自动创建一个错误对应的异常对象，抛出该异常对象

catch：用来捕获 JDK 创建的异常对象，进行后续的处理

```java
try {
	
} catch(Exception e){
	
}
```

```java
public class Test3 {
    public static void test3(String str){
        Integer integer = null;
        try {
            integer = Integer.valueOf(str);
        } catch (Exception e){
            System.out.println(e.getMessage());
        }
    }
}
```

业务代码出错的概率比较大的时候，一般需要加上 try-catch 来进行异常的处理，如果是出错概率较小的代码，不需要加 try-catch

finally 关键字，无论程序是否抛出异常，finally 代码块中的程序一定会执行

```
try{

} catch(){

} finally{
	
}
```

```java
public class Test {
    public static void main(String[] args) throws Exception {
        System.out.println(test());
    }

    public static int test(){
        try {
            System.out.println("try");
            return 10;
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            System.out.println("finally...");
            return 20;
        }
    }
}
```

## throw 和 throws

throw 和 throws 是 Java 在处理异常时使用的两个关键字，都是用来抛出异常的，但是使用方式和表示的含义完全不同。

Exception 是父类，IOException 是子类，只能把子类对象赋值给父类，但是不能将父类对象赋值给子类，是多态的一种体现。

Java 中抛出异常的方式有 3 种：

- try-catch，是一种防范机制，代码可能会出现异常，如果抛出则捕获即可，如果不抛出则程序继续执行
- throw 开发者主动创建一个错误并抛出

```java
public class Test {
    public static void main(String[] args) throws Exception {
        String str = "Java";
        if(str.equals("Java")){
            throw new NumberFormatException();
        } else {
            int num = Integer.parseInt(str);
        }
    }
}
```

- throws 标注方法的，用来描述该方法可能会抛出的异常

throw 主动抛出一个对象，throws 标注一个方法可能会抛出某些异常

```java
public class Test {
    public static void main(String[] args) throws Exception {
        try {
            test();
        } catch (NumberFormatException e) {
            e.printStackTrace();
        }
    }

    public static void test() throws NumberFormatException{
        String str = "Java";
        int num = Integer.parseInt(str);
    }
}
```

通过 throws 可以声明方法，其他人在调用方法的时候必须强制其使用 try-catch 进行代码处理

## 自定义异常

定义一个方法，对传入的参数进行 ++ 操作并返回结果，同时要求参数必须是整数类型，如果传入的参数不是整数类型则抛出自定义异常

自定义的类继承 Exception ，就成为了一个异常类

```java
public class NumberException extends Exception {
    public NumberException(String message){
        super(message);
    }
}
```

```java
public class Test {
    public static void main(String[] args) {
        Test test = new Test();
        try {
            int add = test.add("a");
            System.out.println(add);
        } catch (NumberException e) {
            e.printStackTrace();
        }
    }

    public int add(Object object) throws NumberException{
        if(!(object instanceof Integer)) {
            //抛出异常
            //异常信息是传入的参数不是整数类型
            throw new NumberException("传入的参数不是整数类型");
        } else {
            int num = (int) object;
            return ++num;
        }
    }
}
```

# 多线程

使用多线程可以让程序充分利用 CPU 的资源，提高 CPU 的使用效率，从而解决高并发所带来的负载均衡问题

优点：

- 资源得到更合理的利用
- 程序设计更加简洁
- 程序响应速度更快，运行效率更高

缺点：

- 需要更多的内存空间来支持多线程
- 多线程并发访问的情况可能会影响数据的准确性
- 数据被多线程共享，可能会出现死锁的情况

应该将程序设计得更加合理有效，避免多线程得缺点，充分发挥多线程得优点，从而提高程序得性能

## 进程和线程

什么是进程

进程就是计算机正在运行的一个独立的应用程序，进程是一个动态的概念。

什么是线程

线程是组成进程的基本单位，一个进程中包含一个或多个线程，线程可以完成特定的功能

进程和线程都是应用程序在执行过程中的概念，动态，如果应用程序没有执行，则不存在进程和线程

应用程序是静态的概念，进程和线程都是动态的概念，有创建就有销毁，存在也是暂时的，不是永久的。

进程和线程的区别？

进程在运行时拥有独立的内存空间，每个进程所占用的内存都是独立的，互不干扰。

多个线程是共享内存空间的，但是每个线程的执行是相对独立的，只不过共用内存空间。

线程必须依赖于进程才能执行，单独的线程是无法执行的，由进行来控制多个线程的执行。

多线程：一个进程中，多个线程同时执行。

单核 CPU 的情况下，多线程并不是真正的同时执行，而是多个线程交替占用 CPU ，执行自己的业务

因为程序执行速度太快，看起来是在同时执行。

多核 CPU 的情况下，多线程才是真正的同时执行

```java
public class Test {
    public static void main(String[] args) {
        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                System.out.println("++++++++++++"+i);
            }
        }).start();

        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                System.out.println(i + "============");
            }
        }).start();
    }
}
```



