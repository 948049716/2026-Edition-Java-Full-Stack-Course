# 多线程

## sleep 和 wait

sleep 和 wait 的功能类似，都是让线程暂停执行任务。

sleep 是 Thread 类提供的方法，wait 是 Object 类提供的方法

sleep 是直接作用于线程对象本身，wait 作用于线程正在访问的资源

调用 A 对象的 wait：让当前正在访问 A 对象的线程休眠，同时它有一个前提，当前线程必须拥有 A 对象，所以 wait 方法只能在同步方法或同步代码块中调用，否则会抛出异常。

wait 释放锁，sleep 不会释放锁。

```java
import java.util.concurrent.TimeUnit;

public class Test {
    public static void main(String[] args) {
        A a = new A();
        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                a.test(i);
            }
        }).start();
    }
}

class A{
    public synchronized void test(int i){
        if(i == 5){
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(i + "---------------");
    }
}
```

i == 5，调用 A 的 wait 方法，会让正在访问 A 的线程休眠，并且永久不会解除阻塞。

如何让线程解除阻塞？

1、指定 wait 的时间，wait(long millis)，millis 之后会自动解除阻塞，类似 sleep

wait -》Object

A 继承了 Object，A 中自带 wait 方法

sleep -》Thread

```java
import java.util.concurrent.TimeUnit;

public class Test {
    public static void main(String[] args) {
        A a = new A();
        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                a.test(i);
            }
        }).start();
    }
}

class A{
    public synchronized void test(int i){
        if(i == 5){
            try {
                wait(3000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(i + "---------------");
    }
}
```

2、通过调用 notify 方法唤醒线程

```java
import java.util.concurrent.TimeUnit;

public class Test {
    public static void main(String[] args) {
        A a = new A();
        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                a.test(i);
            }
        }).start();

        new Thread(()->{
            try {
                TimeUnit.SECONDS.sleep(7);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            a.test2();
        }).start();
    }
}

class A{
    public synchronized void test(int i){
        if(i == 5){
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(i + "---------------");
    }

    public synchronized void test2(){
        this.notify();
    }
}
```

无论 wait 还是 notify 方法，都必须放到同步方法或者同步代码块中才能正常调用，否则会抛出异常。

## synchronized 锁定的是谁

> 如果 synchronized 修饰非静态方法，则锁定的是方法的调用者

```java
import java.util.concurrent.TimeUnit;

public class Test {
    public static void main(String[] args) {
        Data data = new Data();
        new Thread(()->{
            data.func1();
        },"A").start();

        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        new Thread(()->{
            data.func3();
        },"B").start();
    }
}

class Data {
    public synchronized void func1(){
        try {
            TimeUnit.SECONDS.sleep(3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("1...");
    }

    public synchronized void func2(){
        System.out.println("2...");
    }

    public void func3(){
        System.out.println("3...");
    }
}
```

首先判断方法是否添加了 synchronized 关键字，如果没有添加，则不需要考虑线程同步的问题，如果添加了，则需要考虑线程同步的问题，看当前锁定的资源在内存中有几份，如果只有一份，则多个线程会同步，如果有多份，则多个线程不需要同步。

> 如果 synchronized 修饰的是静态方法，则锁定的是类，无论有多少个对象，都会同步

```java
import java.util.concurrent.TimeUnit;

public class Test {
    public static void main(String[] args) {
        Data data1 = new Data();
        new Thread(()->{
            data1.func1();
        },"A").start();

        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        Data data2 = new Data();
        new Thread(()->{
            data2.func2();
        },"B").start();
    }
}

class Data {
    public synchronized static void func1(){
        try {
            TimeUnit.SECONDS.sleep(3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("1...");
    }

    public synchronized static void func2(){
        System.out.println("2...");
    }
}
```

> 如果 synchronized 静态方法和实例方法同时存在，静态方法锁定的是类，实例方法锁定的是对象

```java
import java.util.concurrent.TimeUnit;

public class Test {
    public static void main(String[] args) {
        Data data = new Data();
        new Thread(()->{
            data.func1();
        },"A").start();

        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        new Thread(()->{
            data.func2();
        },"B").start();
    }
}

class Data {
    public synchronized static void func1(){
        try {
            TimeUnit.SECONDS.sleep(3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("1...");
    }

    public synchronized void func2(){
        System.out.println("2...");
    }
}
```

> 如果 synchronized 修饰的是代码块，则锁定的是传入的对象

```java
import java.util.concurrent.TimeUnit;

public class Test2 {
    public static void main(String[] args) {
        Data2 data2 = new Data2();

        for (int i = 0; i < 5; i++) {
            A a = new A();
            new Thread(()->{
                data2.func(a);
            }).start();
        }
    }
}

class Data2{
    public void func(A a){
        synchronized (a){
            System.out.println("start...");
            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("end...");
        }
    }
}

class A{

}
```

## ConcurrentModificationException 并发修改异常

ArrayList 是线程不安全的集合，当多个线程同时操作集合时，会出现数据不准确的情况

如何解决？

1、将 ArrayList 替换成线程安全的集合 Vector

```java
public boolean add(E e) {
    ensureCapacityInternal(size + 1);  // Increments modCount!!
    elementData[size++] = e;
    return true;
}

public synchronized boolean add(E e) {
        modCount++;
        ensureCapacityHelper(elementCount + 1);
        elementData[elementCount++] = e;
        return true;
    }
```

2、使用 Collections.synchronizedList

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.Vector;
import java.util.concurrent.TimeUnit;

public class Test {
    public static void main(String[] args) {
        List<String> list = Collections.synchronizedList(new ArrayList<>());
        for (int i = 0; i < 10; i++) {
            new Thread(()->{
                try {
                    TimeUnit.MILLISECONDS.sleep(1);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                list.add("a");
                System.out.println(list);
            }).start();
        }
    }
}
```

3、JUC 工具类 CopyOnWriteArrayList

java.util.concurrent JDK 的一个包，存放都是关于并发的工具类

写时复制

```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.concurrent.CopyOnWriteArrayList;
import java.util.concurrent.TimeUnit;

public class Test {
    public static void main(String[] args) {
        List<String> list = new CopyOnWriteArrayList<>();
        for (int i = 0; i < 10; i++) {
            new Thread(()->{
                try {
                    TimeUnit.MILLISECONDS.sleep(1);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                list.add("a");
                System.out.println(list);
            }).start();
        }
    }
}
```

CopyOnWrite 写时复制，当我们往一个容器中添加元素的时候，不直接往当前容器中添加，而是将当前容器进行复制，向新容器中添加元素，添加完成之后，再将原容器的引用指向新的容器。

```java
public boolean add(E e) {
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
        Object[] elements = getArray();
        int len = elements.length;
        Object[] newElements = Arrays.copyOf(elements, len + 1);
        newElements[len] = e;
        setArray(newElements);
        return true;
    } finally {
        lock.unlock();
    }
}
```

可以对 CopyOnWrite 容器进行并发的读，而不需要加锁，因为当前容器不会添加任何元素，添加元素都是针对复制出来的新集合进行操作，所以 CopyOnWrite 容器也是一种读写分离的思想，读和写操作的是不同的容器。

