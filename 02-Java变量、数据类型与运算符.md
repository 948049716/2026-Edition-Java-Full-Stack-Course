# 变量

1、声明变量的数据类型和变量名

Java 中有多少种数据类型？

Java 有两大类数据类型

- 基本数据类型（不是对象，可以通过包装类将它改造成对象，8 种）
- 引用数据类型（对象，JDK 类库、框架类库、开发者自定义类，无数种）

Java 开发中使用的对象共有三种来源

- JDK 类库 String Date Integer
- 第三方类库 框架 Application SpringBootApplication DispatcherServlet
- 自定义的类

2、变量的值

```java
int i = 1;
String str = new String("Hello World");
```

基本数据类型和引用类型在内存中的区别

所有的变量都是存储在栈内存中的，无论是基本数据类型还是引用类型，基本数据类型和引用数据类型的区别在于是否使用了堆内存

基本数据类型不需要使用堆内存，直接将变量的值存入栈内存

引用类型需要使用堆内存，对象是创建在堆内存中的，将堆内存的地址引用赋值给变量

# 基本数据类型

数值类型：byte、short、int、long、float、double

非数值类型：char、boolean

byte 空间 1 个字节

byte KB MB GB TB

1KB = 1024 byte

1MB = 1024 KB

1GB = 1024 MB

1TB = 1024 GB

1 个字节 = 8 位二进制

short 空间 2 个字节

int 空间 4 个字节

long 空间 8 个字节

float 空间 4 个字节

double 空间 8 个字节

char 空间 2 个字节

boolean 空间 几个字节？

1/8 个字节 1位 1bit

bit

1 byte = 8 bit

0/1

boolean 表示逻辑运算的结果，真或者假

0 表示假 false

1 表示真 true

# 数据类型的转换

1、基本数据类型的转换

- 自动转换
- 强制转换

尽量不使用强制类型转换，因为存在数据精度损失的隐患

```java
double num = 10.5;
int i = (int)num;
```

byte -> short -> int -> long -> float -> double

从小向大转

2、引用数据类型的转换 

前提是需要转换的两种数据类型必须有继承的关系

- 自动转换
- 强制转换

```java
public class Test {
    public static void main(String[] args) {
        Student student = new Student();
        Person person = new Person();
        //自动类型转换
        person = student;
        //强制类转转换
        student = (Student) person;
    }
}
```

实现类和接口也可以进行类型转换，因为接口和实现类本质上也是父类和子类的继承关系

接口就是父类，实现类就是子类

接口是由抽象类演变而来的

一个类中包含抽象方法

抽象父类

```java
public abstract class Test1 {
    public abstract void test();
}
```

非抽象子类

```java
public class Test2 extends Test1 {
    @Override
    public void test() {

    }
}
```

抽象父类可以优化成接口

```java
public interface Test1 {
    public abstract void test();
}
```

非抽象子类变成实现类

```java
public class Test2 implements Test1 {
    @Override
    public void test() {

    }
}
```

接口不能进行强制类型转换，因为接口无法实例化

接口和抽象类的区别

- 关键字不同
- 抽象类中可以包含非抽象方法，接口中全部是抽象方法
- 具体化的方式不同，接口通过实现类，抽象类通过继承
- 抽象类单继承，接口多实现

父类中的构造器，在创建子类对象的时候会自动调用，非抽象类，调用构造器来创建对象，创建父类对象

抽象类，调用父类构造器，但不会创建对象

# 运算符

逻辑运算符只能用于 boolean 类型的数据运算，判断 boolean 数据之间的逻辑关系，包括与、或、非

与：&&（短路与）、&

或：||（短路或）、|

非：!

与：A 和 B，A 和 B 都为 true，结果为 true，否则为 false

或：A 和 B，有一个为 true，结果为 true，否则为 false

非：取反 

与和短路与的区别，运算逻辑完全一致，短路与的性能更高

或和短路或的区别，运算逻辑完全一致，短路或的性能更高

表达式 A & 表达式 B

同时运算表达式 A 和表达式 B，对结果进行逻辑与运算

表达式 A && 表达式 B

优先运算表达式 A，如果结果为 false，则不需要运算表达式 B

表达式 A | 表达式 B

同时运算表达式 A 和表达式 B，对结果进行逻辑与运算

表达式 A || 表达式 B

优先运算表达式 A，如果结果为 true，则不需要运算表达式 B

```java
public class Test {
    public static void main(String[] args) {
        int num1 = 10;
        int num2 = 11;
        System.out.println((num1++ == num2) && (++num1 == num2));
        System.out.println(num1);
    }
}
```

