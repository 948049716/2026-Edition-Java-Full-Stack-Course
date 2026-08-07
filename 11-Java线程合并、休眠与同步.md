# 多线程

## 线程合并

合并是指将指定的某个线程加入到当前线程中，合并为一个线程。

由两个线程交替执行变成一个线程中的两个子线程顺序执行，一个线程执行完毕之后再来执行第二个线程，通过 join 方法来实现合并。

线程甲和线程乙，线程甲在执行到某个时间点的时候调用线程乙的 join() 方法，则表示从当前时间点开始 CPU 资源被线程乙独占，线程甲进入阻塞状态，直到线程乙执行完毕，线程甲继续获取 CPU 资源执行自己的任务。

谁调用 join，将谁合并到对方的线程中

```java
public class Test {
    public static void main(String[] args) {
        JoinRunnable joinRunnable = new JoinRunnable();
        Thread thread = new Thread(joinRunnable);
        thread.start();

        for (int i = 0; i < 100; i++) {
            if(i == 10){
                try {
                    thread.join();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            System.out.println(i + "+++++++++++++++++main");
        }
    }
}
```

join() 方法存在重载 join(long millis)

区别？

通过 join 进行合并，则合并进来的线程会一直占用 CPU 资源，直到自己的任务结束才会释放 CPU，另外一个线程才能执行。

通过 join(long millis) 进行合并，则合并进来的线程不会一直占用 CPU 资源，根据 millis 时间来占用，时间到了之后无论线程是否执行完毕，都会将 CPU 释放出来，回到两个线程争夺 CPU 资源的情况。

```java
public class Test {
    public static void main(String[] args) {
        JoinRunnable joinRunnable = new JoinRunnable();
        Thread thread = new Thread(joinRunnable);
        thread.start();

        for (int i = 0; i < 100; i++) {
            if(i == 10){
                try {
                    thread.join(5000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            System.out.println(i + "+++++++++++++++++main");
        }
    }
}
```

## 线程休眠

通过调用 sleep 方法进行休眠，多线程的场景中，调用 sleep 是让哪个线程休眠？

不是谁调用 sleep 就让谁休眠，而是看在哪调的，在哪个线程中调用 sleep 方法就让哪个线程休眠，和调用者无关。

```java
public class Test {
    public static void main(String[] args) {
        Thread thread = new Thread(()->{
            try {
                Thread.sleep(5000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            for (int i = 0; i < 100; i++) {
                System.out.println(i + "========================");
            }
        });
        thread.start();



        for (int i = 0; i < 100; i++) {
            System.out.println("++++++++++++++++++++++++++++++main" + i);
        }
    }
}
```

```java
public class Test {
    public static void main(String[] args) {
        Thread thread = new Thread(()->{
            for (int i = 0; i < 100; i++) {
                System.out.println(i + "========================");
            }
        });
        thread.start();

        try {
            Thread.sleep(5000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        for (int i = 0; i < 100; i++) {
            System.out.println("++++++++++++++++++++++++++++++main" + i);
        }
    }
}
```

## 线程同步

多线程同时访问同一个资源，可能会导致数据不准确的情况出现，所以需要通过线程同步来解决这一问题。

同步和异步的区别

同步是指多个线程按顺序一个接一个执行，排队执行

异步是指多个线程同时执行

通过给目标方法加锁的方式来解决

```java
public class Account implements Runnable {

    private static int num;

    @Override
    public synchronized void run() {
        num++;
        try {
            Thread.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(Thread.currentThread().getName() + "是当前的第" + num + "位访客");
    }
}
```

synchronized 可以修饰实例方法，也可以修饰静态方法，但是两者在使用上是有区别的

```java
public class SynchronizedTest {
    public static void main(String[] args) {
        for (int i = 0; i < 5; i++) {
            Thread thread = new Thread(()->{
                SynchronizedTest.test();
            });
            thread.start();
        }
    }

    public synchronized static void test(){
        System.out.println("start...");
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("end...");
    }
}
```

如何判断多个线程是否会实现同步，看锁定的资源在内存中有几份，如果只有一份，多线程肯定需要排队，如果有多份，则多线程不需要排队，就不会实现同步。

synchronized 还可以修饰代码块，会为代码块加上锁，从而实现同步

包装类常量池有一个可用范围：-128~127，在此区间内常量池可用，同一个数值可以给多个线程使用，一旦超出这个范围，则常量池失效，此时就需要单独在堆中创建对应的数据，不是一个对象而是多个对象



