# 数组

为什么数组下标都是从 0 开始的？

提高检索速度，取出数据更快

数组的内存模型相关

数组常见的异常 数组下标越界，ArrayIndexOutOfBoundsException

## 数组常用操作以及方法

- 求数组的最大值
- 求数组的最小值
- 在数组的指定位置插入一个数据
- 数组排序

```java
import java.util.Arrays;

public class Test {
    public static void main(String[] args) {
        int[] array = {100,96,93,87,73,62};
        //求最大值
        int max = array[0];
        for (int i = 1; i < array.length; i++) {
            if(array[i] > max){
                max = array[i];
            }
        }
        System.out.println(max);

        //求最小值
        int min = array[0];
        for (int i = 1; i < array.length; i++) {
            if(array[i] < min){
                min = array[i];
            }
        }
        System.out.println(min);

        //在指定位置插入元素
        //将 83 插入到下标为 3 的位置
        /**
         * 1、创建长度+1的新数组
         * 2、导入数据
         * 3、插入新数据
         */
        int[] array2 = new int[array.length+1];
        for (int i = 0; i < 3; i++) {
            array2[i] = array[i];
        }
        for (int i = 4; i < array2.length; i++){
            array2[i] = array[i-1];
        }
        array2[3] = 83;
        System.out.println("插入之前的数组：" + Arrays.toString(array));
        System.out.println("插入之后的数组：" + Arrays.toString(array2));

        //将数组升序排列

        System.out.println(Arrays.toString(array));

        //冒泡排序
        for (int j = 0; j < array.length - 1; j++) {
            for (int i = 0; i < array.length-1-j; i++) {
                if(array[i] > array[i+1]){
                    int temp = array[i];
                    array[i] = array[i+1];
                    array[i+1] = temp;
                }
            }
        }


//        for (int i = 0; i < array.length-1; i++) {
//            if(array[i] > array[i+1]){
//                int temp = array[i];
//                array[i] = array[i+1];
//                array[i+1] = temp;
//            }
//        }
//
//
//
//        for (int i = 0; i < array.length-2; i++) {
//            if(array[i] > array[i+1]){
//                int temp = array[i];
//                array[i] = array[i+1];
//                array[i+1] = temp;
//            }
//        }
//
//
//        for (int i = 0; i < array.length-3; i++) {
//            if(array[i] > array[i+1]){
//                int temp = array[i];
//                array[i] = array[i+1];
//                array[i+1] = temp;
//            }
//        }
//
//
//        for (int i = 0; i < array.length-4; i++) {
//            if(array[i] > array[i+1]){
//                int temp = array[i];
//                array[i] = array[i+1];
//                array[i+1] = temp;
//            }
//        }
//
//
//        for (int i = 0; i < array.length-5; i++) {
//            if(array[i] > array[i+1]){
//                int temp = array[i];
//                array[i] = array[i+1];
//                array[i+1] = temp;
//            }
//        }

        System.out.println(Arrays.toString(array));


        for (int j = 0; j < array.length - 1; j++) {
            for (int i = 0; i < array.length-1-j; i++) {
                if(array[i] < array[i+1]){
                    int temp = array[i];
                    array[i] = array[i+1];
                    array[i+1] = temp;
                }
            }
        }

        System.out.println(Arrays.toString(array));
    }
}
```

Arrays 工具类常用方法

```java
import java.util.Arrays;

public class Test {
    public static void main(String[] args) {
        int[] array1 = {73,80,62,93,96,87};
        int[] array2 = {73,80,62,93,96,87};
        int[] array3 = {66,55,44,33,22};
        System.out.println(Arrays.equals(array1, array2));
        Arrays.sort(array3);
        System.out.println(Arrays.toString(array3));
        Arrays.fill(array2, 66);
        System.out.println(Arrays.toString(array2));
        int[] copyOf = Arrays.copyOf(array3, 10);
        System.out.println(Arrays.toString(copyOf));
        Arrays.sort(array1);
        System.out.println(Arrays.binarySearch(array1, 87));
    }
}
```

## 二维数组

创建一个数组，保存 6 个货柜中所有的商品，每个货柜有 10 件商品

二维数组就是一个一维数组，一维数组中存储的值是另一个一维数组

```java
import java.util.Arrays;

public class Test {
    public static void main(String[] args) {
        int[][] array = {{1,2,3},{4,5,6},{7,8,9}};
        for (int i = 0; i < array.length; i++) {
            for (int i1 = 0; i1 < array[i].length; i1++) {
                System.out.println(array[i][i1]);
            }
        }
    }
}
```

二维数组第一维必须指定长度，第二维可以不指定长度，为什么？

因为第一维数组中存储的值是另外任意一个数组的内存地址，长度不确定，所以不需要指定第二维的长度。

上中下，每层 6 个格挡，每个格挡的鸡蛋个数是层数*格挡数，按照此规律使用二维数组来装载所有的鸡蛋并求出总数量。

```java
public class Test {
    public static void main(String[] args) {
        int[][] array = new int[3][6];
        int sum = 0;
        for (int i = 0; i < array.length; i++) {
            System.out.println("--------------第" + (i + 1) + "层货柜----------------");
            for (int j = 0; j < 6; j++) {
                int num = (j+1)*(i+1);
                System.out.println("第" + (j + 1) + "个格挡的鸡蛋个数：" + num);
                sum += num;
            }
        }
        System.out.println("鸡蛋的总数是：" + sum);
    }
}
```