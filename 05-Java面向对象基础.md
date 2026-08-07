# Java 面向对象

面向对象是一种编程思想

面向过程是另外一种编程思想，具体到每一个行为，按顺序执行

北京、车、驾驶员

# 类和对象

面向对象编程最基础的元素就是对象，对象从何而来？通过类来创建的

类 -》对象

类是所有对象的抽象模板，对象是类的一个具体实例

编写的代码都是类，静态资源，程序真正运行时，JVM 会加载类，运行类中代码，从而产生对象

对象的存在是一种瞬时状态，程序执行期间对象是存在的，程序一旦结束对象就不存在了，而类是一直存在的

类中包含两部分内容，用来描述对象的特征

- 静态特征 颜色、重量、编号...
- 动态特征 带有动作执行的操作 走路 跑步 

静态特征在类中定义为属性（成员变量）

动态特征在类中定义为方法

return 是结束方法的关键字

return 10 结束方法的同时向外返回 10

```java
public class Student {
    //属性的定义包含三部分内容
    //访问权限修饰符、数据类型、属性名
    public int id;
    public String name;

    //方法的定义包含五部分内容
    //访问权限修饰符、返回值类型、方法名、参数列表、方体
    public void test(int i){
        if(i == 1){
            System.out.println(123);
            return;
        }
        System.out.println(456);
        System.out.println(789);
    }

    public int add(int num1,int num2){
        int num3 = num1+num2;
        return num3;
    }
}
```

类是创建对象的，对象是通过构造函数来创建的，类中必须有至少一个构造函数，每个类都有一个默认的无参构造函数，但是一旦在类中声明一个有参构造，则默认的无参构造函数会被覆盖而导致无法调用创建对象。

构造函数不需要返回值的定义，方法名必须和类名保持一致，可以有参数列表，构造函数访问权限修饰符。

无参构造和有参构造的区别？

无参构造只能创建对象，无法给对象的属性赋值

有参构造可以连同创建对象 + 属性赋值一起完成

```java
public class Student {

    public int id;
    public String name;

    public Student(int id,String name){
        this.id = id;
        this.name = name;
    }

    public Student(){

    }

    @Override
    public String toString() {
        return "Student{" +
                "id=" + id +
                ", name='" + name + '\'' +
                '}';
    }
}
```

```java
public class Test {
    public static void main(String[] args) {
        //创建一个id=1，name=张三的对象
        Student student = new Student();
        student.id = 1;
        student.name = "张三";
        System.out.println(student);
        //通过有参构造来创建
        Student student1 = new Student(1, "张三");
        System.out.println(student1);
    }
}
```

# this 关键字

this 用来指代当前类的实例化对象，通过 this 可以调用当前类的属性和方法，比如有参构造中，通过 this 将外部传来的值赋给当前类的实例化对象。

```java
public Student(int id,String name){
    this.id = id;
    this.name = name;
}
```

this 除了可以在类中访问属性也可以在类中调用方法，类中的方法分为两种：构造方法和普通方法

用 this 调用这两类方法的语法也不相同

调用构造函数：this(参数列表)

调用普通方法：this.方法名(参数列表)

不能在普通方法中使用 this 来调用构造函数

# 方法重载

方法重载是 Java 代码复用的一种重要方式，指的是两个方法之间的一种关系

- 在同一个类中
- 方法名相同
- 参数列表不同（个数或数据类型不同）
- 与返回值和访问权限修饰符无关

```
A public int test(){}
  public int test(){}
  
B public double test(double a,double b){}
  public double test(){}
  
C public String test(){}
  public void test(int a){}
  
D public void test(){}
  public void test2(){}
```

# 成员变量和局部变量

两个变量之间除了数据类型、变量名、变量值不同之外，变量的作用域范围也是不同的。

变量的作用域是指在程序中可以通过变量名来访问该变量的范围

变量的作用域由变量被声明时所在的位置决定

根据不同的作用域可以将变量分为成员变量和局部变量

如果一个变量在方法中声明，则该变量为局部变量

如果一个变量在方法外，类中声明，则该变量为成员变量

```java
public class Student {
    //成员变量
    public int id;
    
    public void test(){
        //局部变量
        int num = 1;
    }

}
```

成员变量的作用域是整个类，类中的任意一个方法都可以访问该变量，而局部变量的作用域仅在该方法中，其他方法无法调用该变量

局部变量没有默认值，成员变量有默认值

# 封装

为什么要有封装？

为了保证数据的安全性

将类的信息全部封装到内部，让外部无法直接访问，确保数据的安全

1、将属性私有化（private）

2、通过间接的方式让外部访问内部的私有属性

```java
public class Student {
    //成员变量
    private String name;
    private int age;

    public void setAge(int age){
        if(age < 0){
            System.out.println("年龄不能小于0，已重新赋值");
            this.age = 1;
        } else {
            this.age = age;
        }
    }

    public void setName(String name){
        this.name = name;
    }

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```