# 日期类

## Date

| 标记 | 描述                                         |
| ---- | -------------------------------------------- |
| y    | 年，yyyy 表示 4 位数的年份信息               |
| M    | 月，MM 表示 2 位数的月份信息                 |
| m    | 分钟，mm 表示 2 位数的分钟信息               |
| d    | 天，dd 表示 2 位数的天信息                   |
| H    | 小时，HH 表示 2 位数的 24 小时制下的小时信息 |
| h    | 小时，hh 表示 2 位数的 12 小时制下的小时信息 |
| s    | 秒，ss 表示 2 位数的秒信息                   |

```java
import java.text.SimpleDateFormat;
import java.util.*;

public class Test {
    public static void main(String[] args) {
        Date date = new Date();
        System.out.println(date);
        //2024-01-01 20:00:00
        SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        String format = dateFormat.format(date);
        System.out.println(format);
    }

}
```

## Calendar

| 常量                                 | 描述           |
| ------------------------------------ | -------------- |
| public static final int YEAR         | 年             |
| public static final int MONTH        | 月             |
| public static final int DAY_OF_MONTH | 天，以月为单位 |
| public static final int DAY_OF_YEAR  | 天，以年为单位 |
| public static final int HOUR_OF_DAY  | 小时           |
| public static final int MINUTE       | 分钟           |
| public static final int SECOND       | 秒             |
| public static final int MILLISECOND  | 毫秒           |

| 方法                                 | 描述                               |
| ------------------------------------ | ---------------------------------- |
| public static Calendar getInstance() | 获取系统对应的 Calendar 实例化对象 |
| public void set(int field,int value) | 给静态常量赋值                     |
| public int get(int field)            | 取出静态常量                       |
| public final Date getTime()          | 获取 Calendar 对应的 Date 对象     |

```java
import java.text.SimpleDateFormat;
import java.util.*;

public class Test {
    public static void main(String[] args) {
        //当天所在的周是当年的第几周
        Calendar calendar = Calendar.getInstance();
        calendar.set(Calendar.YEAR, 2024);
        //月份从0开始
        calendar.set(Calendar.MONTH,4);
        calendar.set(Calendar.DAY_OF_MONTH, 6);
        int i = calendar.get(Calendar.WEEK_OF_MONTH);
        System.out.println(i);

        //今天之后的 300 天的日期
        int i1 = calendar.get(Calendar.DAY_OF_YEAR);
        calendar.set(Calendar.DAY_OF_YEAR, i1+300);
        Date time = calendar.getTime();
        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd");
        String format = simpleDateFormat.format(time);
        System.out.println(format);
    }
}
```

# IO 流

Input 输入、Output 输出

## File 类

| 方法                               | 描述                         |
| ---------------------------------- | ---------------------------- |
| public File(String path)           | 根据路径创建文件对象         |
| public String getName()            | 获取文件名                   |
| public String getParent()          | 获取文件所在的目录           |
| public File getParentFile()        | 获取文件所在的目录对应的对象 |
| public String getPath()            | 获取文件路径                 |
| public boolean exists()            | 判断对象是否存在             |
| public boolean isDirectory()       | 判断对象是否为目录           |
| public boolean isFile()            | 判断对象是否为文件           |
| public long length()               | 获取文件的大小               |
| public boolean createNewFile()     | 根据当前对象创建文件         |
| public boolean delete()            | 删除对象                     |
| public boolean mkdir()             | 创建文件夹                   |
| public boolean renameTo(File file) | 为已存在的文件重命名         |

```java
import java.io.File;
import java.io.IOException;
import java.text.SimpleDateFormat;
import java.util.*;

public class Test {
    public static void main(String[] args) {
//        File file = new File("D:\\java\\test.txt");
//        if (file.exists()) {
//            System.out.println(file + "存在");
//            System.out.println(file.getName());
//            System.out.println(file.getPath());
//            System.out.println(file.isFile());
//            File file1 = new File(file.getParent());
//            System.out.println(file1.isDirectory());
//            long length = file.length();
//            System.out.println(length);
//        } else {
//            System.out.println(file + "不存在，自动创建");
//            try {
//                file.createNewFile();
//            } catch (IOException e) {
//                e.printStackTrace();
//            }
//        }
        File file = new File("D:\\java\\test2.txt");
        if(file.exists()){
            boolean delete = file.delete();
            if(delete){
                System.out.println("删除成功");
            } else {
                System.out.println("删除失败");
            }
        }
    }
}
```

## 字节流

- 按照方向分，输入流和输出流
- 按照单位分，字节流和字符流，字节流指每次处理的数据是以字节为单位，字符流指每次处理数据以字符为单位

输入字节流和输出字节流

输入字节流 InputStream

| 方法                                          | 描述                                         |
| --------------------------------------------- | -------------------------------------------- |
| public int read()                             | 以字节为单位读取数据                         |
| public int read(byte[] bytes)                 | 将数据存入 byte 数组，返回数据长度           |
| public int read(byte[] bytes,int off,int len) | 将数据存入 byte 数组的指定区间，返回数据长度 |
| public int available()                        | 返回当前数据流中未读取的数据个数             |
| public void close()                           | 关闭数据流                                   |

```java
import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;
import java.io.InputStream;
import java.text.SimpleDateFormat;
import java.util.*;

public class Test {
    public static void main(String[] args) throws Exception {
        InputStream inputStream = new FileInputStream("D:\\java\\test.txt");
        byte[] bytes = new byte[10];
        int length = inputStream.read(bytes,2,3);
        System.out.println(length);
        for (byte aByte : bytes) {
            System.out.println(aByte);
        }
        int temp = 0;
        while ((temp = inputStream.read()) != -1){
            System.out.println(temp);
        }
    }
}
```

输出字节流 OutputStream

| 方法                                        | 描述                             |
| ------------------------------------------- | -------------------------------- |
| public void write(int b)                    | 以字节为单位写数据               |
| public void write(byte[] b)                 | 将 byte 数组中的数据写出         |
| public void write(byte[] b,int off,int len) | 将 byte 数组中指定区间的数据写出 |
| public void close()                         | 关闭数据流                       |

```java
import java.io.*;
import java.text.SimpleDateFormat;
import java.util.*;

public class Test {
    public static void main(String[] args) throws Exception {
        OutputStream outputStream = new FileOutputStream("D:\\java\\test2.txt");
//        outputStream.write(98);
        byte[] bytes = {97,98,99,100,101,102};
        outputStream.write(bytes,2,3);
        outputStream.close();
    }
}
```

## 文件复制

```java
import java.io.*;
import java.text.SimpleDateFormat;
import java.util.*;

public class Test {
    public static void main(String[] args) throws Exception {
        InputStream inputStream = new FileInputStream("D:\\java\\test.txt");
        OutputStream outputStream = new FileOutputStream("D:\\java\\copy2.txt");
//        int temp = 0;
//        while ((temp = inputStream.read()) != -1){
//            outputStream.write(temp);
//        }
        byte[] bytes = new byte[1024];
        int length = inputStream.read(bytes);
        outputStream.write(bytes,0,length);
        inputStream.close();
        outputStream.close();
    }
}
```