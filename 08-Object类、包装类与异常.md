# Object 方法重写

## toString

以字符串的形式返回该类的实例化对象信息

```java
public String toString() {
    return getClass().getName() + "@" + Integer.toHexString(hashCode());
}
```

## equals 

equals 方法是用来判断两个对象是否相等

```java
public boolean equals(Object obj) {
    return (this == obj);
}
```

== 判断的是对象的内存地址

```java
@Override
public boolean equals(Object o) {
    if (this == o) return true;
    if (o == null || getClass() != o.getClass()) return false;
    User user = (User) o;
    return Objects.equals(id, user.id) &&
        Objects.equals(name, user.name);
}
```

name 必须用 equals 来判断，id 必须用 equals 来判断

String 类对于 equals 方法的重写

```java
public boolean equals(Object anObject) {
    if (this == anObject) {
        return true;
    }
    if (anObject instanceof String) {
        String anotherString = (String)anObject;
        int n = value.length;
        if (n == anotherString.value.length) {
            char v1[] = value;
            char v2[] = anotherString.value;
            int i = 0;
            while (n-- != 0) {
                if (v1[i] != v2[i])
                    return false;
                i++;
            }
            return true;
        }
    }
    return false;
}
```

Integer 类对于 equals 方法的重写

```java
public boolean equals(Object obj) {
    if (obj instanceof Integer) {
        return value == ((Integer)obj).intValue();
    }
    return false;
}
```

== 判断两个对象的内存地址是否相等

equals 默认是 Object 类提供的方法，默认实现和 == 是一致的

如果是默认的 equals(Object 中原生的方法)，则和 == 是一样的，没有区别，因为 Object 中方法的实现就是 ==

如果是重写的 equals，则和 == 是不一样的，重写之后判断的就是对象的值是否一致，具体实现需要看具体的类型

## hashCode

```java
public native int hashCode();
```

native 修饰的本地方法，Java 无法实现的功能，由其他语言（C++）来实现，Java 只需要负责调用

hashCode 包含内存地址、对象的属性值、类的信息，混合在一起，映射出来的一个整形数值

hashCode 和 equals 需要配合起来使用，都是用来判断两个对象是否相等

hashCode 效率更高，如果两个对象的 hashCode 不相等，则这两个对象肯定不相等，但是如果两个对象的 hashCode 相等，不能说明两个对象肯定相等，此时就需要使用 equals 进一步验证

先用效率高的方法来判断，如果能得到结果，则直接返回，如果无法得到结果，再用效率低的方法进行验证

集合框架中使用

元素唯一

# 包装类

## 什么是包装类

Java 中的数据类型从本质上分为两类：8 种基本数据类型和引用类型

如何把基本数据类型转成对象？

包装类是 Java 提供的一组类，专门用来创建 8 种基本数据类型对应的对象

| 基本数据类型 | 包装类    |
| ------------ | --------- |
| byte         | Byte      |
| short        | Short     |
| int          | Integer   |
| long         | Long      |
| float        | Float     |
| double       | Double    |
| char         | Character |
| boolean      | Boolean   |

包装类全部存放于 java.lang 包中

一级父类：Object

二级父类：Character、Number、Boolean

Number 的子类：Byte、Short、Integer、Long、Float、Double

实际开发中对于包装类的使用就是将基本数据类型转为包装类，或者将包装类转为基本数据类型

## 装箱和拆箱

装箱：将基本数据类型转为包装类

```java
public class Test {
    public static void main(String[] args) {
        byte b = 1;
        Byte byt = new Byte(b);
        short s = 2;
        Short shor = new Short(s);
        int i = 3;
        Integer integer = new Integer(i);
        long l = 4;
        Long lon = new Long(l);
        float f = 5.5f;
        Float flo = new Float(f);
        double d = 6.6;
        Double dou = new Double(d);
        char cha = 'J';
        Character charac = new Character(cha);
        boolean bo = true;
        Boolean bool = new Boolean(bo);
    }
}
```

拆箱：将包装类对象转为基本数据类型

```java
public class Test {
    public static void main(String[] args) {
        byte b = 1;
        Byte byt = new Byte(b);
        byte b1 = byt.byteValue();
        
        short s = 2;
        Short shor = new Short(s);
        short i1 = shor.shortValue();

        int i = 3;
        Integer integer = new Integer(i);
        int i2 = integer.intValue();

        long l = 4;
        Long lon = new Long(l);
        long l1 = lon.longValue();

        float f = 5.5f;
        Float flo = new Float(f);
        float v = flo.floatValue();

        double d = 6.6;
        Double dou = new Double(d);
        double v1 = dou.doubleValue();
        
        char cha = 'J';
        Character charac = new Character(cha);
        char c = charac.charValue();
        
        boolean bo = true;
        Boolean bool = new Boolean(bo);
        boolean b2 = bool.booleanValue();
    }
}
```

# 异常

## 什么是异常

Java 中的错误可以分为两类：

- 编译时错误，一般指语法错误
- 运行时错误，语法没有问题，可以正常通过编译，但是运行时报错

Java 专门提供了一组类，来表示各种各样的运行时错误

- ArithmeticException：表示数学异常

```java
System.out.println(10 / 0);
```

- ClassNotFoundException：类未定义异常

```java
public static void main(String[] args) throws Exception {
    System.out.println(Class.forName("Test2"));
}
```

- IllegalArgumentException：参数格式异常

```java
import java.lang.reflect.Method;

public class Test {
    public static void main(String[] args) throws Exception {
        Class<?> test = Class.forName("Test");
        Method method = test.getMethod("test", Integer.class);
        method.invoke(new Test(), "1");
    }

    public void test(Integer integer){
        System.out.println(integer);
    }
}
```

- ArrayIndexOutOfBoundsException：数组下标越界异常

```java
public class Test {
    public static void main(String[] args) throws Exception {
        int[] array = {1,2,3};
        System.out.println(array[3]);
    }
}
```

- NullPointerException：空指针异常

```java
public class Test {
    public static void main(String[] args) throws Exception {
        Integer num = null;
        System.out.println(num.equals(1));
    }
}
```

- NoSuchMethodException：方法未定义异常

```java
import java.lang.reflect.Method;

public class Test {
    public static void main(String[] args) throws Exception {
        Class<?> test = Class.forName("Test");
        Method method = test.getMethod("test2", Integer.class);
        System.out.println(method);
    }

    public void test(Integer integer){
        System.out.println(integer);
    }
}
```

- NumberFormatException：将其他数据类型转为数值类型的不匹配异常

```java
public class Test {
    public static void main(String[] args) throws Exception {
       Integer integer = new Integer("a");
        System.out.println(integer);
    }
}
```