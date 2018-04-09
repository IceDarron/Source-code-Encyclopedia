# 基本概念
SortedMap保证按照键的升序排列的映射，对entrySet、keySet和values方法返回的结果进行迭代时，顺序就会反映出来。

```java
public interface SortedMap<K,V> extends Map<K,V> {
    //...
    // 用来排序的comparator
    Comparator<? super K> comparator();
}
```