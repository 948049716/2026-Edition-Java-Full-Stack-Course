# JUC

## ForkJoin 框架

ForkJoin 框架是 JDK1.7 之后提供的一个多线程并发处理框架，本质上是对线程池的一种补充，它的核心思想是将一个大型任务拆分成多个小任务，分别执行，最终将小任务的结果进行汇总，形成最终的结果。

拆分

多个任务 + 多个线程

- ForkJoinTask 表示任务
- ForkJoinPool 表示线程（线程池的一种扩展）

## 递归

什么是递归？

定义：编程语言中，函数直接或间接调用函数本身，则该函数称为递归，一个方法自己调自己

电影院求自己是第几排的问题，递推公式：

```java
f(n) = f(n-1)+1,f(1)=1
```

递归代码：

```java
int f(int n){
    if(n==1) return 1;
	return f(n-1)+1;
}
```

递归需要满足 3 要素：

1、一个父问题可以拆分成若干个子问题，并且若干个子问题的结果汇总起来就是父问题的结果

2、父问题和子问题，解题思路完全一致，只是数据规模不同

3、存在终止条件

假如有 n 个台阶，每次你可以跨 1 个台阶或者 2 个台阶，请求 n 个台阶一共有多少种走法？

可以根据第一步的走法将所有走法分为两类

第一类是第一步走了一个台阶，第二类是第一步走了两个台阶

n 个台阶的走法就等于先走 1 个台阶后，n-1 个台阶的走法 + 先走 2 个台阶，n-2 个台阶的走法

递推公式：

```
f(1) = 1
f(2) = 2
f(n) = f(n-1) + f(n-2)
```

终止条件

f(1) = 1

f(2) = 2

n = 1,f(1) =1

n = 2,f(2) = 2

n = 3,f(3) = f(2) + f(1) = 3

n = 4,f(4) = f(3) + f(2) = 5

递归代码：

```java
int f(int n){
	if(n == 1) return 1;
	if(n == 2) return 2;
	return f(n-1) + f(n-2);
}
```



1、创建一个 ForkJoinTask 任务，ForkJoinTask 是一个抽象类，需要创建一个类来继承 ForkJoinTask 的子类RecursiveTask，实现抽象方法 compute，拆分的逻辑就写在 compute 方法中。

2、任务要通过 ForkJoinPool 来执行，将任务直接放入 ForkJoinPool 中，直接获取结果即可。

计算 0~20 亿数字相加之和

```java
import java.util.concurrent.RecursiveTask;

public class ForkJoinDemo extends RecursiveTask<Long> {

    private Long start;
    private Long end;
    private Long temp = 100_0000L;

    public ForkJoinDemo(Long start,Long end){
        this.start = start;
        this.end = end;
    }

    @Override
    protected Long compute() {
        if((end - start) < temp){
            Long sum = 0L;
            for (Long i = start;i<=end;i++){
                sum += i;
            }
            return sum;
        } else {
            Long avg = (start + end)/2;
            ForkJoinDemo task1 = new ForkJoinDemo(start, avg);
            task1.fork();
            ForkJoinDemo task2 = new ForkJoinDemo(avg+1, end);
            task2.fork();
            return task1.join() + task2.join();
        }
    }
}
```

```java
import java.util.concurrent.ExecutionException;
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.ForkJoinTask;

public class Test {
    public static void main(String[] args) {
        Long startTime = System.currentTimeMillis();
        ForkJoinPool forkJoinPool = new ForkJoinPool();
        ForkJoinTask<Long> task = new ForkJoinDemo(0L, 20_0000_0000L);
        forkJoinPool.execute(task);
        Long sum = 0L;
        try {
            sum = task.get();
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
        Long endTime = System.currentTimeMillis();
        System.out.println(sum + ",耗时" + (endTime - startTime) + "毫秒");
    }
}
```

## volatile 关键字

```java
public class SingletonDemo {
    private volatile static SingletonDemo instance;
    public SingletonDemo(){
        System.out.println("创建了一个SingletonDemo对象");
    }
    public static SingletonDemo getInstance(){
        if(instance == null){
            synchronized (SingletonDemo.class){
                if(instance == null) {
                    instance = new SingletonDemo();
                }
            }
        }
        return instance;
    }
}
```

volatile 关键字的作用是可以使内存中的数据对线程可见

Java 内存模型 JMM Java Memory Model

一个线程在访问内存数据的时候，其实不是拿到数据本身，而是将数据复制保存到工作内存中，相当于使用的是一个副本，对工作内存中的数据进行修改，修改完成之后再保存到主内存中，主内存对线程不可见。

```java
import java.util.concurrent.TimeUnit;

public class Test3 {
    private static int num = 0;
    public static void main(String[] args) {
        new Thread(()->{
            while (num == 0){

            }
        }).start();

        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        num = 1;
        System.out.println(num);
    }
}
```

结果输出 1，但是循环没有停止，因为主内存对线程不可见，子线程从主内存中取出 num=0 放入到工作内存，主线程也从主内存中取出 num = 0 放入工作内存，执行 num = 1，然后将 num = 1 还回到主内存中，但是此时，子线程的工作内存中的 num = 0，所以循环不会结束。

```java
import java.util.concurrent.TimeUnit;

public class Test3 {
    private static volatile int num = 0;
    public static void main(String[] args) {
        new Thread(()->{
            while (num == 0){
                System.out.println("---Thread---");
            }
        }).start();

        try {
            TimeUnit.SECONDS.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        num = 1;
        System.out.println(num);
    }
}
```

# 集合框架

什么是集合？

多个对象，个数未知，类型未知

集合可以简单理解为一个长度可以改变，可以保存任意数据类型的动态数组

在 Java 中，集合不是由一个类来完成的，而是由一组接口和类共同构成了一个框架体系，大致可分为 3 层，最上层是一组接口，继而是接口的实现类，接下来就是对集合各种操作的工具类

| 接口         | 描述                                                         |
| ------------ | ------------------------------------------------------------ |
| Collection   | 集合框架最基础的接口，一个 Collection 存储一组无序、不唯一的对象，一般不直接使用该接口 |
| List         | Collection 的子接口，存储一组有序、不唯一的对象，开发中常用的接口之一 |
| Set          | Collection 的子接口，存储一组无序、唯一的对象                |
| Map          | 独立于 Collection 的另外一个接口，存储一组键值对象、提供键到值的映射 |
| Iterator     | 专用用来输出集合元素的接口，一般适用于无序集合，从前向后单向输出元素 |
| ListIterator | Iterator 的子接口，可以双向输出集合中的元素                  |
| Enumeration  | 传统的输出接口，已经被 Iterator 所取代                       |
| SortedSet    | Set 的子接口，可以对集合中的元素进行排序                     |
| SortedMap    | Map 的子接口，可以对集合中的键值元素进行排序                 |
| Queue        | 队列接口，此接口的实现类可以实现队列操作                     |
| Map.Entry    | Map 的内部接口，描述 Map 中的一个键值对元素                  |