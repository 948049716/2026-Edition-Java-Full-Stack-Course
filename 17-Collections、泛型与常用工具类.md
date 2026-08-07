# Collections 工具类

Collections 工具类专门用来操作集合的，添加元素、对元素进行排序、替换元素

Collections 常用方法

| 方法                                                         | 描述                                           |
| ------------------------------------------------------------ | ---------------------------------------------- |
| public static void sort(List list,Comparator)                | 根据Comparator接口进行排序                     |
| public static int binarySearch(List list,Object k)           | 查找元素在集合中的下标，要求集合必须是升序排列 |
| public static Object get(int index)                          | 根据下标找到元素                               |
| public static void reverse(List list)                        | 对集合元素的顺序进行反转                       |
| public static void swap(List list,int i,int j)               | 交换两个元素的位置                             |
| public static void fill(List list,Object o)                  | 将集合中的元素全部替换为o                      |
| public static void min(List list)                            | 返回集合中的最小值                             |
| public static void max(List list)                            | 返回集合中的最大值                             |
| public static boolean replaceAll(List list,Object oldV,Object newV) | 将集合中所有的 oldV 替换为 newV                |
| public static boolean allAll(List list,Object... o)          | 向集合中添加元素                               |

```java
import java.util.*;

public class Test {
    public static void main(String[] args) {
        ArrayList list = new ArrayList();
        list.add("He");
        list.add("Wor");
        System.out.println(list);
        Collections.addAll(list, "Java","JavaS","JavaME","JavaSEE","JavaSSEE");
        System.out.println(list);
        Collections.reverse(list);
        System.out.println(list);
        Collections.swap(list, 1, 2);
        System.out.println(list);
        Collections.sort(list);
        System.out.println(list);
        int javaSE = Collections.binarySearch(list, "World");
        System.out.println(javaSE);
        Collections.replaceAll(list, "Java","Test");
        System.out.println(list);
        Collections.sort(list, new Comparator<String>() {

            //1 o1>o2
            //0 o1=o2
            //-1 o1<o2
            @Override
            public int compare(String o1, String o2) {
                if(o1.length() > o2.length()) return 1;
                if(o1.length() < o2.length()) return -1;
                return 0;
            }
        });
        System.out.println(list);
    }

}
```

# 泛型

泛型是指在类定义时不指定类中信息的具体数据类型，而是用一个标识符来替代，当外部实例化对象的时候再来指定具体的数据类型。

多态的思想

```java
public class Time<T> {
    private T value;

    public T getValue() {
        return value;
    }

    public void setValue(T value) {
        this.value = value;
    }

    public Time(T value) {
        this.value = value;
    }
}
```

```java
import java.util.*;

public class Test {
    public static void main(String[] args) {
        Time<String> time = new Time("十点");
        String value = time.getValue();
        Time<Integer> time1 = new Time<>(10);
        Integer value1 = time1.getValue();
        Time<Float> time2 = new Time<>(10.0f);
        Float value2 = time2.getValue();
    }

}
```

```java
public class Time<H,M,S> {
    private H hour;
    private M minute;
    private S second;

    public H getHour() {
        return hour;
    }

    public void setHour(H hour) {
        this.hour = hour;
    }

    public M getMinute() {
        return minute;
    }

    public void setMinute(M minute) {
        this.minute = minute;
    }

    public S getSecond() {
        return second;
    }

    public void setSecond(S second) {
        this.second = second;
    }
}
```

```java
import java.util.*;

public class Test {
    public static void main(String[] args) {
        Time<String,Integer,Float> time = new Time();
        time.setHour("十点");
        time.setMinute(10);
        time.setSecond(10.0f);
        System.out.println("现在的时间是:" + time.getHour() + ":" + time.getMinute() + ":" + time.getSecond());
    }

}
```

# 枚举

Enum 是一种有确定取值区间的数据类型，本质上是一种类，具有简洁、高效、安全、方便等特点

枚举的值被约束到一个特定的范围，只能从该范围中取值

某个类的对象取值范围是确定的，预先创建好一部分对象，使用的时候从这个范围中取值即可，可以使用枚举进行简化

```java
public enum WeekEnum {
    MONDAY,TUESDAY,WEDNESDAY,THURSDAY,FRIDAY,SATURDAY,SUNDAY;
}
```

```java
public enum WeekEnum {
    MONDAY(0,"周一"),
    TUESDAY(1,"周二"),
    WEDNESDAY(2,"周三"),
    THURSDAY(3,"周四"),
    FRIDAY(4,"周五"),
    SATURDAY(5,"周六"),
    SUNDAY(6,"周天");

    WeekEnum(Integer code, String value) {
        this.code = code;
        this.value = value;
    }

    private Integer code;
    private String value;

    public Integer getCode() {
        return code;
    }

    public void setCode(Integer code) {
        this.code = code;
    }

    public String getValue() {
        return value;
    }

    public void setValue(String value) {
        this.value = value;
    }

    @Override
    public String toString() {
        return "WeekEnum{" +
                "code=" + code +
                ", value='" + value + '\'' +
                '}';
    }
}
```

# Math

```java
import java.util.*;

public class Test {
    public static void main(String[] args) {
        System.out.println(Math.PI);
        System.out.println(Math.sqrt(9));
        System.out.println(Math.cbrt(8));
        System.out.println(Math.pow(2, 3));
        System.out.println(Math.min(1, 2));
        System.out.println(Math.max(3, 10));
        System.out.println(Math.abs(-10));
        System.out.println(Math.ceil(10.0001));
        System.out.println(Math.floor(10.9999));
        System.out.println(Math.random());
        System.out.println(Math.round(5.6));
    }

}
```

# Random

Random 是用来产生一个随机数的，并且可以指定任意的区间，在此区间产生一个随机数。

| 方法                         | 描述                         |
| ---------------------------- | ---------------------------- |
| public Random()              | 创建一个无参的随机数构造器   |
| public Random(long seed)     | 使用 long 数据创建一个随机数 |
| public boolean nextBoolean() | 创建一个随机的 boolean 值    |
| public double nextDouble()   | 创建一个随机的 double 值     |
| public float nextFloat()     | 创建一个随机的 float 值      |
| public int nextInt()         | 创建一个随机的 int 值        |
| public long nextLong()       | 创建一个随机的 long 值       |

```java
import java.util.*;

public class Test {
    public static void main(String[] args) {
        Random random = new Random();
        for (int i = 0; i < 3; i++) {
            int b = random.nextInt(10);
            System.out.println(b);
        }
    }

}
```

# StringBuffer

String 的底层是用数组来存值的，数组长度不可变

StringBuffer 和 String 类似，底层也是一个数组，但是是一个可以扩容的动态数组

StringBuffer 默认的数组长度为 16

| 方法                                          | 描述                         |
| --------------------------------------------- | ---------------------------- |
| public StringBuffer()                         | 创建一个空的StringBuffer     |
| public StringBuffer(String str)               | 创建一个 str 的 StringBuffer |
| public int length()                           | 获取长度                     |
| public StringBuffer append(String str)        | 追加字符串                   |
| public StringBuffer delete(int start,int end) | 删除指定区间内的字符串       |

```java
import java.util.*;

public class Test {
    public static void main(String[] args) {
        StringBuffer stringBuffer = new StringBuffer("Hello World");
        StringBuffer java = stringBuffer.append("Java");
        System.out.println(java);
        StringBuffer delete = stringBuffer.delete(3, 6);
        System.out.println(delete);
        StringBuffer reverse = stringBuffer.reverse();
        System.out.println(reverse);
        StringBuffer six = stringBuffer.insert(6, "six");
        System.out.println(six);
    }

}
```