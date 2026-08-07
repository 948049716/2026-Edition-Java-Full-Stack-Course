# 集合

## Collection 接口

Collection 是集合框架中最基础的父接口，可以存储一组无序、不唯一的对象

Iterable 专门用来迭代，把集合中的元素全部取出来

Collection 中的所有元素可以通过 Iterator 进行迭代（List、Set）

Collection 常用方法

| 方法                              | 描述                                   |
| --------------------------------- | -------------------------------------- |
| int size()                        | 获取集合长度                           |
| boolean isEmpty()                 | 判断集合是否为空                       |
| boolean contains(Object o)        | 判断集合中是否包含某个元素             |
| Iterator<E> iterator()            | 实例化 Iterator 接口，遍历集合         |
| Object[] toArray()                | 将集合转换为一个 Object 类型的对象数组 |
| boolean add(E e)                  | 向集合中添加元素                       |
| boolean remove(Object o)          | 从集合中移除元素                       |
| boolean containsAll(Collection c) | 判断集合中是否存在某个集合的所有元素   |
| boolean addAll(Collection c)      | 向集合中添加某个集合的所有元素         |
| boolean removeAll(Collection c)   | 从集合中移除满足条件的另一个集合       |
| void clear()                      | 清空集合                               |
| boolean equals(Object o)          | 比较两个集合是否相等                   |
| int hashCode()                    | 获取集合对象的散列值                   |

## Collection 的子接口

使用其子接口

- List：存放有序、不唯一的元素
- Set：存放无序、唯一的元素
- Queue：队列接口

## List 接口

List 接口在继承 Collection 接口的基础上进行了扩展，常用的扩展方法

| 方法                                       | 描述                               |
| ------------------------------------------ | ---------------------------------- |
| E get(int index)                           | 通过下标获取集合中指定位置的元素   |
| E set(int index,E element)                 | 替换集合中指定位置的元素           |
| void add(int index.E element)              | 向集合中的指定位置添加元素         |
| E remove(int index)                        | 通过下标删除集合中指定位置的元素   |
| int indexOf(Object o)                      | 查找某个对象在集合中的下标         |
| int lastIndexOf(Object o)                  | 从后向前查找某个对象在集合中的下标 |
| ListIterator<E> listIterator()             | 实例化 ListIterator 接口           |
| List<E> subList(int fromIndex,int toIndex) | 获取集合中的子集合                 |

## List 接口的实现类

ArrayList 实现了长度可变的数组

优点：查找快，因为元素都是连续的，可以快速求出下标从而取出元素

缺点：增删慢，因为元素都是连续的，添加一个元素，就必须先将后续的元素依次后移一位，再把新元素加入，删除同理

```java
public String toString() {
    Iterator<E> it = iterator();
    if (! it.hasNext())
        return "[]";

    StringBuilder sb = new StringBuilder();
    sb.append('[');
    for (;;) {
        E e = it.next();
        sb.append(e == this ? "(this Collection)" : e);
        if (! it.hasNext())
            return sb.append(']').toString();
        sb.append(',').append(' ');
    }
}
```

ArrayList 常用方法

```java
import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;

public class Test {
    public static void main(String[] args) {
        ArrayList list = new ArrayList();
        list.add("Hello");
        list.add("World");
        list.add("JavaSE");
        list.add("JavaME");
        list.add("JavaEE");
        System.out.println(list.toString());
        System.out.println(list.size());
        System.out.println(list.contains("Java"));
        Iterator iterator = list.iterator();
        while (iterator.hasNext()){
            Object next = iterator.next();
            System.out.println(next);
        }
        System.out.println("********************************");
        for (int i = 0; i < list.size(); i++) {
            System.out.println(list.get(i));
        }
        list.remove(0);
        System.out.println("*************************************");
        System.out.println(list);
        list.remove("JavaSE");
        System.out.println(list);
        list.add("a");
        System.out.println(list);
        list.add(1, "b");
        System.out.println(list);
        list.set(1, "c");
        System.out.println(list);
        System.out.println(list.indexOf("cc"));
        List list1 = list.subList(1, 3);
        System.out.println(list1);
    }
}
```

LinkedList 采用链表的形式来存储数据，和 ArrayList 恰好相反

优点：增删快

缺点：查询慢

```java
import java.util.ArrayList;
import java.util.Iterator;
import java.util.LinkedList;
import java.util.List;

public class Test {
    public static void main(String[] args) {
        LinkedList list = new LinkedList();
        list.add("Hello");
        list.add("World");
        list.add("Java");
        System.out.println(list);
        list.offer("JavaSE");
        list.offer("JavaSE2");
        System.out.println(list);
        list.push("JavaME");
        System.out.println(list);
        list.addFirst("JavaME2");
        System.out.println(list);
        System.out.println(list.peekFirst());
        System.out.println(list.peekLast());
        System.out.println(list);
        System.out.println(list.pollFirst());
        System.out.println(list.pollLast());
        System.out.println(list);
    }
}
```

## Set 接口

Set 接口是 Collection 的子接口，Set 接口以散列的形式存储数据，所以元素是无序的，可以存储一组无序且唯一的对象。

## Set 接口的实现类

HashSet 存储一组无序且唯一的元素，这里的无序是指元素的存储顺序和遍历顺序不一致。

```java
import java.util.*;

public class Test {
    public static void main(String[] args) {
        HashSet hashSet = new HashSet();
        hashSet.add("Hello");
        hashSet.add("World");
        hashSet.add("Java");
        hashSet.add("Hello");
        System.out.println(hashSet.size());
        Iterator iterator = hashSet.iterator();
        while (iterator.hasNext()){
            System.out.println(iterator.next());
        }
        hashSet.remove("World");
        Iterator iterator1 = hashSet.iterator();
        while (iterator1.hasNext()){
            System.out.println(iterator1.next());
        }
    }
}
```

LinkedHashSet 是 Set 的另外一个子接口，可以存储一组有序且唯一的元素，这里的有序是指元素的存储顺序和遍历顺序是一致。

```java
import java.util.*;

public class Test {
    public static void main(String[] args) {
        LinkedHashSet set = new LinkedHashSet();
        set.add("Hello");
        set.add("World");
        set.add("Java");
        set.add("Hello");
        System.out.println(set.size());
        Iterator iterator = set.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
        set.remove("World");
        System.out.println(set);
    }
}
```

```java
import java.util.*;

public class Test {
    public static void main(String[] args) {
        LinkedHashSet set = new LinkedHashSet();
        set.add(new A(1));
        set.add(new A(1));
        Iterator iterator = set.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
    }
}

class A{
    private int num;

    public A(int num) {
        this.num = num;
    }

    @Override
    public String toString() {
        return "A{" +
                "num=" + num +
                '}';
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        A a = (A) o;
        return num == a.num;
    }

    @Override
    public int hashCode() {
        return Objects.hash(num);
    }
}
```

TreeSet 存储一组有序、唯一的元素，TreeSet 的有序和 LinkedHashSet 的有序不一样。

LinkedHashSet 的有序是指元素的存储顺序和遍历顺序一致，TreeSet 的有序是指集合内部会自动给所有的元素按照升序进行排列。

```java
import java.util.*;

public class Test {
    public static void main(String[] args) {
        TreeSet treeSet = new TreeSet();
        treeSet.add(new A(1));
        treeSet.add(new A(3));
        treeSet.add(new A(6));
        treeSet.add(new A(2));
        treeSet.add(new A(5));
        treeSet.add(new A(4));
        treeSet.add(new A(1));
        Iterator iterator = treeSet.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
    }
}

class A implements Comparable{
    private int num;

    public A(int num) {
        this.num = num;
    }

    /**
     * A.compareTo(B)
     * 1表示A大于B
     * 0表示A等于B
     * -1表示A小于B
     * @param o
     * @return
     */
    @Override
    public int compareTo(Object o) {
        A a = (A)o;
        if(this.num < a.num){
            return 1;
        }
        if(this.num > a.num){
            return -1;
        }
        return 0;
    }

    @Override
    public String toString() {
        return "A{" +
                "num=" + num +
                '}';
    }
}
```

## Map 接口

Map 接口是与 Collection 接口完全独立的另一个体系。

区别在于 Set、List、Collection 只能操作单个元素，Map 可以操作一对元素，Map 中的元素都是以 key-value 的形式进行存储的。

| 方法                                | 描述                              |
| ----------------------------------- | --------------------------------- |
| int size()                          | 获取集合长度                      |
| boolean isEmpty()                   | 判断集合是否为空                  |
| boolean containsKey(Object key)     | 判断集合中是否存在 key            |
| boolean containsValue(Object value) | 判断集合中是否存在 value          |
| V get(Object key)                   | 通过 key 取出对应的 value         |
| V put(Object key,Object value)      | 向集合中添加一组 key-value        |
| V remove(Object key)                | 通过 key 删除集合元素             |
| void clear()                        | 清空集合                          |
| Set keySet()                        | 取出所有的 key，返回 Set          |
| Collection values()                 | 取出所有的 value，返回 Collection |
| Set entrySet()                      | 将 Map 转换为 Set 对象            |
| int hashCode()                      | 获取集合的散列码                  |
| boolean equals(Object o)            | 比较两个集合是否相等              |

## Map 接口的实现类

- HashMap：存储一组无序、key 不可重复，value 可重复的元素
- Hashtable：存储一组无序、key 不可重复，value 可以重复的元素
- TreeMap：存储一组有序、key 不可重复、value 可以重复的元素，可以按照 key 来排序

HashMap 常用方法

```java
import java.util.Collection;
import java.util.HashMap;
import java.util.Iterator;
import java.util.Set;

public class Test {
    public static void main(String[] args) {
        HashMap hashMap = new HashMap();
        hashMap.put("h", "Hello");
        hashMap.put("w", "World");
        hashMap.put("j", "Java");
        hashMap.put("s", "JavaSE");
        hashMap.put("m", "JavaME");
        hashMap.put("e", "JavaEE");
        System.out.println(hashMap);
        hashMap.remove("e");
        System.out.println(hashMap);
        hashMap.put("m", "Java");
        System.out.println(hashMap);
        System.out.println(hashMap.containsKey("a"));
        System.out.println(hashMap.containsValue("Java"));
        Set set = hashMap.keySet();
        Iterator iterator = set.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
        Collection values = hashMap.values();
        Iterator iterator1 = values.iterator();
        while (iterator1.hasNext()) {
            System.out.println(iterator1.next());
        }
        Set set1 = hashMap.entrySet();
        Iterator iterator2 = set1.iterator();
        while (iterator2.hasNext()) {
            System.out.println(iterator2.next());
        }
    }
}
```

Hashtable 常用方法

```java
import java.util.*;

public class Test {
    public static void main(String[] args) {
        Hashtable hashtable = new Hashtable();
        hashtable.put("h", "Hello");
        hashtable.put("w", "World");
        hashtable.put("j", "Java");
        hashtable.put("s", "JavaSE");
        hashtable.put("m", "JavaME");
        hashtable.put("e", "JavaEE");
        System.out.println(hashtable);
        hashtable.remove("e");
        System.out.println(hashtable);
        hashtable.put("m", "Model");
        System.out.println(hashtable);
        System.out.println(hashtable.containsKey("a"));
        System.out.println(hashtable.containsValue("Java"));
        Set set = hashtable.keySet();
        Iterator iterator = set.iterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next());
        }
        Collection values = hashtable.values();
        Iterator iterator1 = values.iterator();
        while (iterator1.hasNext()) {
            System.out.println(iterator1.next());
        }
    }
}
```

Hashtable 是线程安全的，HashMap 是线程不安全的

