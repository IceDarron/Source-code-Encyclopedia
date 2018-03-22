List集合

# 基本概念
有序，可索引下角标。支持随机访问，初始容量10。该接口继承了Collection接口。线程不安全的类。

# 主要提供方法
除了Collection接口的方法外，提供了以下关键方法：
+ int indexOf(Object o); 返回该列表中指定元素第一次出现的索引，如果该列表不包含元素，则返回-1。
+ int lastIndexOf(Object o); 返回该列表中指定元素最后一次出现的索引，如果该列表不包含元素，则返回-1。
+ E get(int index); 返回该集合指定位置的元素。
+ E set(int index, E element); 替换集合中指定位置的元素。
+ List<E> subList(int fromIndex, int toIndex); 截取区间集合。区间左闭右开。
                                               对于修改原始集合，相对应subList返回的结果也修改。 
                                               对于返回的集合调用clear操作，原始集合只会清除掉截取部分。
+ void sort(Comparator<? super E> c); 按指定的顺序排序此列表。
```java
default void sort(Comparator<? super E> c) {
        Object[] a = this.toArray();
        Arrays.sort(a, (Comparator) c);
        ListIterator<E> i = this.listIterator();
        for (Object e : a) {
            i.next();
            i.set((E) e);
        }
    }
```
+ ListIterator<E> listIterator(); 返回ListIterator迭代器。
+ ListIterator<E> listIterator(int index); 返回ListIterator迭代器。可以指定迭代开始位置。

# List接口主要实现类
+ java.util.LinkedList（unsynchronized）
+ java.util.ArrayList（unsynchronized）
+ java.util.Vector(synchronized)

# 实现List接口的关键抽象类
+ java.util.AbstractList
