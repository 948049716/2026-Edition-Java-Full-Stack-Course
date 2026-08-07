# JUC 并发编程工具包

## CountDownLatch 

减法计数器，JUC 的工具类，可以用来倒计时，当两个线程同时执行时，如果我们要确保一个线程优先执行

设置一个计数器，当计数器清零的时候，再执行另一个线程

```java
import java.util.concurrent.CountDownLatch;

public class CountDownLatchTest {
    public static void main(String[] args) {
        CountDownLatch countDownLatch = new CountDownLatch(100);
        new Thread(()->{
            for (int i = 0; i < 100; i++) {
                System.out.println("++++++++++++++++++++++Thread");
                countDownLatch.countDown();
            }
        }).start();

        try {
            countDownLatch.await();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        for (int i = 0; i < 100; i++) {
            System.out.println("main------------------------");
        }
    }
}
```

## CyclicBarrier

加法计数器

```java
import java.util.concurrent.BrokenBarrierException;
import java.util.concurrent.CyclicBarrier;

public class CyclicBarrierTest {
    public static void main(String[] args) {
        CyclicBarrier cyclicBarrier = new CyclicBarrier(10,()->{
            System.out.println("放行");
        });

        for (int i = 0; i < 30; i++) {
            final int temp = i;
            new Thread(()->{
                System.out.println("-->" + temp);
                try {
                    cyclicBarrier.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
            }).start();
        }

    }
}
```

## Semaphore

计数信号量，实际开发中主要用来完成限流操作，即限制可以访问某些资源的线程数量。

- 初始化
- 获得许可
- 释放

```java
import java.util.concurrent.Semaphore;
import java.util.concurrent.TimeUnit;

public class SemaphoreTest {
    public static void main(String[] args) {
        Semaphore semaphore = new Semaphore(5);
        for (int i = 0; i < 15; i++) {
            new Thread(()->{
                try {
                    semaphore.acquire();
                    System.out.println(Thread.currentThread().getName() + "进店购物");
                    TimeUnit.SECONDS.sleep(2);
                    System.out.println(Thread.currentThread().getName() + "出店");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    semaphore.release();
                }
            },String.valueOf(i)).start();
        }
    }
}
```

## 读写锁

接口 ReadWriteLock，实现类 ReentrantReadWriteLock，可以多线程同时读，但是同一时间只能有一个线程进行写入操作。

读写锁功能也是为了实现线程同步，只不过粒度更细，可以分别给读和写操作设置不同的锁机制。

```java
import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.locks.ReadWriteLock;
import java.util.concurrent.locks.ReentrantReadWriteLock;

public class ReadWriteLockTest {
    public static void main(String[] args) {
        Cache cache = new Cache();
        for (int i = 0; i < 5; i++) {
            final int temp = i;
            new Thread(()->{
                cache.write(temp, String.valueOf(temp));
            }).start();
        }

        for (int i = 0; i < 5; i++) {
            final int temp = i;
            new Thread(()->{
                cache.read(temp);
            }).start();
        }
    }
}

class Cache{
    private Map<Integer,String> map = new HashMap<>();
    ReadWriteLock lock = new ReentrantReadWriteLock();

    //写操作
    public void write(Integer key,String value){
        lock.writeLock().lock();
        System.out.println(key + "开始写入");
        map.put(key, value);
        System.out.println(key + "写入完毕");
        lock.writeLock().unlock();
    }

    //读操作
    public void read(Integer key){
        lock.readLock().lock();
        System.out.println(key + "开始读取");
        map.get(key);
        System.out.println(key + "读取完毕");
        lock.readLock().unlock();
    }
}
```

## 线程池

预先创建好一定数量的线程对象，存入缓冲池，需要用的时候直接从缓冲池中取出，用完之后不要销毁，还回到缓冲池中，供下一次任务使用。

优点：

- 提高线程的利用率
- 提高响应速度
- 便于统一管理线程对象
- 可控制最大并发量

工作流程：

- 初始化线程池，创建默认数量的线程对象
- 当任务过多的时候，额外补充线程数量
- 当任务趋于正常的时候，额外补充的线程会自动销毁

初始化线程数量

最大线程数量

线程池的实现有 3 种方式

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class Test {
    public static void main(String[] args) {
        //单例线程池，只有一个线程对象
//        ExecutorService executorService = Executors.newSingleThreadExecutor();
//        ExecutorService executorService = Executors.newFixedThreadPool(10);
        ExecutorService executorService = Executors.newCachedThreadPool();
        for (int i = 0; i < 100; i++) {
            final int temp = i;
            executorService.execute(()->{
                System.out.println(Thread.currentThread().getName() + ":" + temp);
            });
        }
        executorService.shutdown();
    }
}
```



```java
public static ExecutorService newSingleThreadExecutor() {
    return new FinalizableDelegatedExecutorService
        (new ThreadPoolExecutor(1, 1,
                                0L, TimeUnit.MILLISECONDS,
                                new LinkedBlockingQueue<Runnable>()));
}
```

```java
public static ExecutorService newFixedThreadPool(int nThreads) {
    return new ThreadPoolExecutor(nThreads, nThreads,
                                  0L, TimeUnit.MILLISECONDS,
                                  new LinkedBlockingQueue<Runnable>());
}
```

```java
public static ExecutorService newCachedThreadPool() {
    return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                  60L, TimeUnit.SECONDS,
                                  new SynchronousQueue<Runnable>());
}
```

ThreadPoolExecutor 线程池的原生类

核心参数：

- corePoolSize：核心池大小
- maximumPoolSize：线程池最大线程数
- keepAliveTime：空闲线程的存活时间
- unit：时间单位
- workQueue：阻塞队列
- threadFactory：线程工厂
- handler：拒绝策略

```java
import java.util.concurrent.*;

public class Test {
    public static void main(String[] args) {
        ExecutorService executorService = new ThreadPoolExecutor(
                2,
                5,
                1L,
                TimeUnit.SECONDS,
                new ArrayBlockingQueue<>(3),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.AbortPolicy()
        );
        for (int i = 0; i < 8; i++) {
            executorService.execute(()->{
                try {
                    TimeUnit.MILLISECONDS.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread().getName() + "===》办理业务");
            });
        }
        executorService.shutdown();
    }
}
```

