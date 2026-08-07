# 运算符

位运算符

对表达式以二进制为单位进行运算，将数据全部转换成二进制再进行运算

十进制和二进制的转换

十进制转二进制：目标数除以二，若能除尽，该位记作 0，若除不尽，则该位记作 1，再对商继续除以二，以此类推，直到商为 0。

10 转为二进制 01010

17 转为二进制 10001

二进制转十进制：从目标数最右侧开始，本位的数值乘以本位的权重（2 的第几位的位数减一次方），把每一位的乘积相加就是十进制

&（按位与）、|（按位或）、^（按位异或）、<<（左移）、>>（右移）

A & B：每一位的数字一一对应，若都为 1，则该位记作 1，否则为 0

A | B：每一位的数字一一对应，只要有 1 个 1，则该位记作 1，否则为 0

A ^ B：每一位的数字一一对应，相同记作 0，不同记作1

A << B：A * 2 的 B 次方，2 << 3 2*2的3次方 = 16

A >> B：A 除以 2 的 B 次方，2 >> 3 2/2的3次方 = 0

10：1010

5：101 

3：11

1001 -》9

# 流程控制

if-else switch-case for while do-while foreach

if-else 和 switch-case 都是进行流程控制的

区别：

1、if-else 可以进行等值判断，也可以进行逻辑判断，switch-case 只能进行等值判断

2、switch-case 代码结构更清晰易懂，if-else 结构相对不清晰

4 种循环

for、while、do-while、foreach（针对集合遍历的循环）增强型 for 循环

循环四要素：

- 初始化循环变量
- 循环条件
- 循环体
- 更新循环变量

```java
public class Test {
    public static void main(String[] args) {
        int i = 0;
        while (i < 100) {
            System.out.println(i);
            i++;
        }
    }
}
```

```java
public class Test {
    public static void main(String[] args) {
        int i = 0;
        do{
            System.out.println(i);
            i++;
        }while (i < 100);
    }
}
```

while 和 do-while 的区别

while 需要先判断循环条件，再执行循环体

do-while 第一次不需要判断循环条件，直接执行循环体，第二次开始判断循环条件

do-while 至少会执行一次，while 有可能一次都不执行

```java
import java.util.Arrays;
import java.util.List;

public class Test {
    public static void main(String[] args) {
        List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
        for (int i = 0; i < list.size(); i++) {
            System.out.println(list.get(i));
        }
        System.out.println("*******************************");
        for (Integer i : list) {
            System.out.println(i);
        }
    }
}
```

循环结束

- 循环逻辑结束
- 循环过程中抛出异常
- 使用 break 关键字中断循环
- 使用 continue 关键字中断循环

break 会结束整个循环，continue 只结束当前循环，继续执行下一次循环

# 数组

数组由 4 种基本元素构成

- 数组名称
- 数组元素
- 元素下标
- 数据类型

对象默认的输出方式

Java 输出对象的时候，默认调用对象的 toString 方法，将结果以字符串的形式进行输出

toString 从何而来？

来自于 Object 类

Java 会有一个根节点，所有的类都是由这个根节点衍生出来的，Java 所有类都有一个默认的父类：Object

多态、复用

都会有共性

打印对象、对象比较、对象的哈希码

![image-20240320215913177](C:\Users\ningn\AppData\Roaming\Typora\typora-user-images\image-20240320215913177.png)

```java
import java.util.Arrays;

public class Test {
    public static void main(String[] args) {
        String[] array = {"a","b","c","d","e","f"};
        System.out.println(array);
        //遍历数组
        for (int i = 0; i < array.length; i++) {
            System.out.println(array[i]);
        }
        System.out.println("********************");
        int l = 0;
        while (l < array.length){
            System.out.println(array[l]);
            l++;
        }
        System.out.println("*************************");
        int m = 0;
        do {
            System.out.println(array[m]);
            m++;
        } while (m < array.length);
        System.out.println("*********************");
        for (String str : array) {
            System.out.println(str);
        }
        System.out.println("***********************");
        System.out.println(Arrays.toString(array));
    }
}
```

