# 多线程

## 线程安全的单例模式

单例模式，其核心思想是一个类只有一个实例对象

如何实现单例模式

1、禁止通过构造器来创建对象，如何实现？

如何禁止外部调用构造器，把构造器私有化（封装）

2、如何确保对象只有一个

同步指线程的状态，多个线程排队执行，加锁是用来实现同步的方法

```java
public class Account {

    private static Account account;

    private Account(){
        System.out.println("创建了Account对象");
    }

    public synchronized static Account getInstance(){
        if(account == null){
            try {
                Thread.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            account = new Account();
        }
        return account;
    }
}
```

## 死锁

死锁：多个线程因为争夺同一个资源而形成的一种互斥状态，导致每个线程都无法继续进行，程序一直处于卡顿状态

```java
public class Data {

}
```

```java
public class DeadLockRunnable implements Runnable {
    public int num;
    private static Data data1 = new Data();
    private static Data data2 = new Data();

    @Override
    public void run() {
        //1号拿着data1，试图获取data2
        //2号拿着data2，试图获取data1
        //谁也不愿意给对方自己的data
        if(num == 1){
            System.out.println(Thread.currentThread().getName() + "获取到了data1，等待获取data2");
            synchronized (data1){
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                synchronized (data2){
                    System.out.println(Thread.currentThread().getName() + "用餐完毕");
                }
            }
        }
        if(num == 2){
            System.out.println(Thread.currentThread().getName() + "获取到了data2，等待获取data1");
            synchronized (data2){
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                synchronized (data1){
                    System.out.println(Thread.currentThread().getName() + "用餐完毕");
                }
            }
        }
    }
}
```

```java
public class DeadLockTest {
    public static void main(String[] args) {
        DeadLockRunnable runnable1 = new DeadLockRunnable();
        runnable1.num = 1;
        DeadLockRunnable runnable2 = new DeadLockRunnable();
        runnable2.num = 2;
        new Thread(runnable1,"张三").start();
        new Thread(runnable2,"李四").start();
    }
}
```

如何避免出现死锁？

```java
public class DeadLockTest {
    public static void main(String[] args) {
        DeadLockRunnable runnable1 = new DeadLockRunnable();
        runnable1.num = 1;
        DeadLockRunnable runnable2 = new DeadLockRunnable();
        runnable2.num = 2;
        new Thread(runnable1,"张三").start();
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        new Thread(runnable2,"李四").start();
    }
}
```

## 重入锁

ReentrantLock 是对 synchronized 的升级，synchronized 是通过 JVM 实现，ReentrantLock 是通过 JDK 实现（用方法调用的形式来使用）

重入锁的特点是可以重复上锁

synchronized 是自动上锁，自动解锁，ReentrantLock 是手动上锁，手动解锁

```java
import java.util.concurrent.locks.ReentrantLock;

public class Account implements Runnable {
    private static int num;
    private ReentrantLock reentrantLock = new ReentrantLock();
    @Override
    public void run() {
        reentrantLock.lock();
        reentrantLock.lock();
        num++;
        try {
            Thread.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(Thread.currentThread().getName() + "是当前的第" + num + "位访客");
        reentrantLock.unlock();
        reentrantLock.unlock();
    }
}
```

```java
public class Test {
    public static void main(String[] args) {
        Account account = new Account();
        Thread thread1 = new Thread(account,"张三");
        Thread thread2 = new Thread(account, "李四");
        thread1.start();
        thread2.start();
    }
}
```

ReentrantLock 还具备限时性的特点，指可以判断某个线程在一定时间内能否获取到锁

tryLock(long timeout,TimeUnit unit)

```java
import java.util.concurrent.TimeUnit;
import java.util.concurrent.locks.ReentrantLock;

public class TimeLock implements Runnable {

    private ReentrantLock reentrantLock = new ReentrantLock();

    @Override
    public void run() {
        try {
            if (reentrantLock.tryLock(6, TimeUnit.SECONDS)) {
                System.out.println(Thread.currentThread().getName() + "获取到了锁");
                Thread.sleep(5000);
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            if(reentrantLock.isHeldByCurrentThread()){
                reentrantLock.unlock();
            }
        }
    }
}
```

```java
public class Test {
    public static void main(String[] args) {
        TimeLock lock = new TimeLock();
        new Thread(lock,"线程1").start();
        new Thread(lock, "线程2").start();
    }
}
```

## 生产者消费者模式

```java
public class Hamburger {
    private int id;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public Hamburger(int id) {
        this.id = id;
    }

    @Override
    public String toString() {
        return "Hamburger{" +
                "id=" + id +
                '}';
    }
}
```

```java
public class Container {
    public Hamburger[] array = new Hamburger[6];
    public int index = 0;
    //向容器中添加汉堡
    public synchronized void push(Hamburger hamburger){
        while (index == array.length){
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        this.notify();
        array[index] = hamburger;
        index++;
        System.out.println("生产了一个汉堡：" + hamburger);
    }
    //从容器中取出汉堡
    public synchronized Hamburger pop(){
        while (index == 0){
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        this.notify();
        index--;
        System.out.println("消费了一个汉堡：" + array[index]);
        return array[index];
    }
}
```

```java
public class Producer implements Runnable {
    private Container container = null;

    public Producer(Container container) {
        this.container = container;
    }

    @Override
    public void run() {
        for (int i = 0; i < 30; i++) {
            Hamburger hamburger = new Hamburger(i);
            this.container.push(hamburger);
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

```java
public class Consumer implements Runnable {
    private Container container = null;

    public Consumer(Container container) {
        this.container = container;
    }

    @Override
    public void run() {
        for (int i = 0; i < 30; i++) {
            this.container.pop();
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

```java
public class Test {
    public static void main(String[] args) {
        Container container = new Container();
        Producer producer = new Producer(container);
        Consumer consumer = new Consumer(container);
        new Thread(producer).start();
        new Thread(producer).start();
        new Thread(consumer).start();
        new Thread(consumer).start();
        new Thread(consumer).start();
    }
}
```