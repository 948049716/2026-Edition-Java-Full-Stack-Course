# 1多态

面向对象三大基本特征：封装、继承、多态

多态是指一个事物有多种表现形态

Java：定义一个类，该类的实例化对象在不同的业务场景种根据不同的需求呈现出不同的业务逻辑

会员买书

普通会员买书 9 折优惠

超级会员买书 6 折优惠

会员 -》收银员

从效果来看提高了代码的扩展性

使用的是多态编程思想，具体是通过继承来实现

## 多态的使用

1、定义方法时形参为父类，调用方法时传入的参数为子类对象

```java
public void setMember(Member member) {
    this.member = member;
}
```

```java
Cashier cashier = new Cashier();
cashier.setMember(new VIPMember());
```

2、定义方法时返回值的数据类型为父类，调用方法时返回子类对象

```java
public Member getMember(String name){
    if(name.equals("ordinaryMember")){
        return new OrdinaryMember();
    }
    if(name.equals("superMember")){
        return new SuperMember();
    }
    if(name.equals("VIPMember")){
        return new VIPMember();
    }
    return null;
}
```

上述两种多态的形式，都是基于子类对象可以直接赋值给父类对象，自动类型转换

# 抽象

方法必须存在，方法的具体实现没有意义

只定义方法，但是不做具体的实现，抽象方法

```java
public abstract void buyBook();
```

抽象方法所在的类，必须定义为抽象类

```java
public abstract class Member {
    public abstract void buyBook();
}
```

抽象类不能被实例化，只能实例化其子类

一旦某个类继承了一个抽象类，要么该类也需要定义为抽象类，要么去重写从抽象父类那里继承过来的抽象方法，让它有具体的实现，不再是抽象方法

一旦某个类中，出现了一个抽象方法，则该类必须被声明为抽象类，但是抽象类可以包含非抽象方法

# 接口

接口是实际开发中使用频繁非常重要的一种编程方式，面向接口编程，提高程序的扩展性，降低程序的耦合度，进行解耦合

接口就是对抽象类的一种升级，实现解耦合

接口本质上就是抽象类，只不过使用更加简便而已

接口是一个极度抽象的抽象类，接口中所有方法必须全部是抽象方法

接口的具体实现叫做实现类

接口对标抽象类

实现类对标抽象类的非抽象子类

```java
public interface Equipment {
    public void work();
}
```

```java
public class EquipmentA implements Equipment {
    public void work(){
        System.out.println("设备A运行，生成产品A");
    }
}
```

```java
public class EquipmentB implements Equipment {
    public void work(){
        System.out.println("设备B运行，生产产品B");
    }
}
```

```java
public class EquipmentC implements Equipment {
    @Override
    public void work() {
        System.out.println("设备C运行，生产产品C");
    }
}
```

```java
public class Factory {
    private Equipment equipment;

    public Equipment getEquipment() {
        return equipment;
    }

    public void setEquipment(Equipment equipment) {
        this.equipment = equipment;
    }

    public void work(){
        System.out.println("开始生产...");
        this.equipment.work();
    }
}
```

```java
public class Test {
    public static void main(String[] args) {
        Factory factory = new Factory();
        factory.setEquipment(new EquipmentC());
        factory.work();
    }
}
```

# Object 类

Object 是 Java 中全部类的共同父类，实现代码复用

Object 中定义了一些所有类都需要使用到的方法，所有类直接继承使用

在具体的业务场景中，子类也可以对从父类继承来的方法进行重写，以匹配当前的业务需求

| 方法                              | 描述                                   |
| --------------------------------- | -------------------------------------- |
| public String toString()          | 以字符串的形式返回该类的实例化对象信息 |
| public boolean equals(Object obj) | 判断两个对象是否相等                   |
| public native int hashCode()      | 返回对象的散列码                       |

