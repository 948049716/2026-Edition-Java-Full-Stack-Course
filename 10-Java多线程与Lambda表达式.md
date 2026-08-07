# 多线程

## Java 中线程的使用

1、继承 Thread 类

Thread 类是 Java 提供的线程的父类

Exception 是 Java 提供的异常的父类

实现程序的扩展，基于 JDK 基础类开发者可以扩展出其他的相关类

对修改封闭（不能修改 JDK 源码），对扩展开放（自定义类，通过继承的形式融入到 JDK 中）

线程一定跟任务绑定，一个线程必须要执行一个任务，一个空的线程没有意义，一个空的任务也没有意义，一定是线程 + 任务绑定在一起

Thread 类是 JDK 线程父类，Runnable 接口是 JDK 定义任务的接口，任务的具体实现写在 run 方法中

1、创建 Thread 对象的时候，从外部传入一个 Runnable 对象（创建线程的时候，需要绑定一个任务）

2、当线程执行的时候，会调用线程的 run 方法

3、如果创建线程对象的时候，传入了任务，则会执行任务，否则线程什么都不做

```java
@Override
public void run() {
    if (target != null) {
        target.run();
    }
}
```

使用线程

1、创建线程对象

2、调用线程对象的 start 方法来启动线程

线程首先是需要去争夺 CPU 资源，当拿到资源之后才能执行任务

```java
public class MyThread extends Thread {
    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            System.out.println(i + "===========");
        }
    }
}
```

```java
public class MyThread2 extends Thread {
    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            System.out.println("+++++++++++++++++" + i);
        }
    }
}
```

```java
public class Test {
    public static void main(String[] args) {
        //创建线程对象
        MyThread thread = new MyThread();
        //启动线程
        thread.start();

        MyThread2 thread2 = new MyThread2();
        thread2.start();
    }
}
```

2、实现 Runnable 接口

第一种形式线程和任务的耦合度过高，不利于程序的扩展，所以开发中不推荐使用

实现解耦合

```java
public class MyRunnable implements Runnable {
    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            System.out.println("~~~~~~~~~~~" + i + "~~~~~~~~~~~~~~");
        }
    }
}
```

```java
public class MyRuunable2 implements Runnable {
    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            System.out.println(i + "******************--------");
        }
    }
}
```

```java
MyRunnable myRunnable = new MyRunnable();
MyRuunable2 myRuunable2 = new MyRuunable2();
Thread thread1 = new Thread(myRunnable);
thread1.start();
```

## 线程的状态

线程一共有 5 种状态，在特定的情况下，线程可以在不同的状态之间切换

- 创建状态：实例化了一个新的线程对象，还未启动
- 就绪状态：创建好的线程对象调用了 start() 方法完成启动，进入线程池等待抢占 CPU 资源
- 运行状态：线程对象获取了 CPU 资源，在规定的时间内执行任务
- 阻塞状态：正在运行的线程暂停执行任务，释放所占用的 CPU 资源
- 终止状态：线程运行完毕或因为异常导致线程终止运行

![image-20240410212216486](C:\Users\ningn\AppData\Roaming\Typora\typora-user-images\image-20240410212216486.png)

## lambda 表达式

函数式编程，将方法的实现作为参数进行传递

可以简化代码的开发

1、通过实现 Runnable 接口的形式来完成线程的使用

```java
public class MyRunnable implements Runnable {
    @Override
    public void run() {
        for (int i = 0; i < 100; i++) {
            System.out.println("~~~~~~~~~~~" + i + "~~~~~~~~~~~~~~");
        }
    }
}
```

```java
public class Test {
    public static void main(String[] args) {
        MyRunnable myRunnable = new MyRunnable();
        Thread thread = new Thread(myRunnable);
        thread.start();
    }
}
```

2、使用内部类对代码进行简化

```java
public class Test {
    public static void main(String[] args) {
        MyRunnable3 myRunnable = new MyRunnable3();
        Thread thread = new Thread(myRunnable);
        thread.start();
    }

    static class MyRunnable3 implements Runnable{
        @Override
        public void run() {
            for (int i = 0; i < 100; i++) {
                System.out.println("~~~~~~~~~~~" + i + "~~~~~~~~~~~~~~");
            }
        }
    }
}
```

3、使用匿名内部类进一步进行简化

```java
public class Test {
    public static void main(String[] args) {
        Runnable myRunnable = new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i < 100; i++) {
                    System.out.println("~~~~~~~~~~~" + i + "~~~~~~~~~~~~~~");
                }
            }
        };
        Thread thread = new Thread(myRunnable);
        thread.start();
    }
}
```

4、使用 lambda 表达式继续进行简化

```java
public class Test {
    public static void main(String[] args) {
        new Thread(()->{
            for (int i = 0; i < 100; i++) {
                System.out.println("~~~~~~~~~~~" + i + "~~~~~~~~~~~~~~");
            }
        }).start();
    }
}
```

将接口作为参数传递的场景下，可以使用 lambda 进行简化

```java
public interface MyRunnable3 {
    public void test();
}
```

```java
public class MyTest {

    public void test(MyRunnable3 myRunnable3){
        myRunnable3.test();
    }

}
```

```java
public class Test {
    public static void main(String[] args) {
        MyTest myTest = new MyTest();
        myTest.test(()->{
            System.out.println(111);
        });
    }
}
```

## 线程调度

### 线程休眠

休眠是指让当前线程暂停执行，从运行状态进入阻塞状态，从而将 CPU 资源让给其他线程的一种调度方式，通过 sleep 方法来实现。

```java
public static native void sleep(long millis) throws InterruptedException;
```

```java
public class Test {
    public static void main(String[] args) {
        new Thread(()->{
            for (int i = 0; i < 100; i++) {
                if(i == 5){
                    try {
                        Thread.sleep(3000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                System.out.println(i + "-----------------");
            }
        }).start();
    }
}
```

sleep 是定义在 Thread 类中的

