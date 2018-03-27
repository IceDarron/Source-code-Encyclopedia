Set

# 基本概念
线程不安全，根据实现方式不同，有的有序，有的无序。元素不重复。

```java
public interface Set<E> extends Collection<E> {
    }
```

# 常用实现类，接口，抽象类
## 线程不安全
+ EnumSet有序不重复
+ TreeSet有序不重复
+ HashSet无序不重复 底层为HashMap
+ LinkedHashSet有序不重复？
+ NavigableSet有序不重复

## 线程安全
+ ConcurrentSkipListSet 实现了NavigableSet接口和继承自AbstractSet的Set集合类，它是线程安全的，内部存储实际是存在ConcurrentNavigableMap中。
+ CopyOnWriteArraySet 底层实现是CopyOnWriteArrayList，线程安全的。


# Set SortedSet NavigableSet 三者关系
```java
public interface Set<E> extends Collection<E> {//不包含重复元素集合

    int size();//元素个数
    boolean isEmpty();// set不包含元素，则返回 true
    boolean contains(Object o);//set包含指定的元素，则返回 true
    Iterator<E> iterator();//此 set中的元素上进行迭代的迭代器
    Object[] toArray();//返回一个包含 set中所有元素的数组
    <T> T[] toArray(T[] a);//返回数组，保留类型
    boolean add(E e);//添加元素
    boolean remove(Object o);//移除元素
    boolean containsAll(Collection<?> c);//是否包含该集合全部元素
    boolean addAll(Collection<? extends E> c);//新增集合全部元素
    boolean retainAll(Collection<?> c);//保留c中的元素
    boolean removeAll(Collection<?> c);//移除c中的元素
    void clear();//清空
    boolean equals(Object o);//相等
    int hashCode();//哈希值
    default Spliterator<E> spliterator(); //并行迭代器
}


public interface SortedSet<E> extends Set<E> {//经过某种排序的set

    Comparator<? super E> comparator();//返回对此 set中的元素进行排序的比较器
    SortedSet<E> subSet(E fromElement, E toElement);//返回此 set的部分视图，其元素从 fromElement（包括）到 toElement（不包括）。（
    SortedSet<E> headSet(E toElement);//返回此 set 的部分视图，其元素严格小于 toElement
    SortedSet<E> tailSet(E fromElement);//返回此 set的部分视图，其元素大于等于 fromElement
    E first();//set中第一个元素
    E last();//set中最后一个元素
    default Spliterator<E> spliterator(); //并行迭代器
}


public interface NavigableSet<E> extends SortedSet<E> {//扩展的 SortedSet，具有了搜索匹配元素方法

    E lower(E e);//返回此 set中小于给定元素的最大元素
    E floor(E e);//返回此 set中小于等于给定元素的最大元素
    E ceiling(E e);//返回此 set中大于等于给定元素的最小元素
    E higher(E e);//返回此 set中大于给定元素的最小元素
    E pollFirst();//获取并移除第一个元素
    E pollLast();//获取并移除最后一个元素
    Iterator<E> iterator();//以升序返回在此 set的元素上进行迭代的迭代器
    NavigableSet<E> descendingSet();//返回此 set中所包含元素的逆序视图
    Iterator<E> descendingIterator();//以降序返回在此 set的元素上进行迭代的迭代器。效果等同于 descendingSet().iterator()。
    NavigableSet<E> subSet(E fromElement, boolean fromInclusive, E toElement, boolean toInclusive);//返回此 set 的部分视图，其元素范围从 fromElement 到 toElement
    NavigableSet<E> headSet(E toElement, boolean inclusive);//返回此 set的部分视图，其元素小于（或等于，如果 inclusive 为 true）toElement
    NavigableSet<E> tailSet(E fromElement, boolean inclusive);//返回此 set的部分视图，其元素大于（或等于，如果 inclusive 为 true）fromElement
    SortedSet<E> subSet(E fromElement, E toElement);//返回此 set 的部分视图，其元素从 fromElement（包括）到 toElement（不包括）。
    SortedSet<E> headSet(E toElement);//返回此 set的部分视图，其元素严格小于 toElement
    SortedSet<E> tailSet(E fromElement);//返回此 set的部分视图，其元素大于等于 fromElement
}
```