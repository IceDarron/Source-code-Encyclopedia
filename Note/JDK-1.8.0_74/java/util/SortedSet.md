# 基本概念
SortedSet接口中的元素一定是有序的。

jdk中只有TreeSet这一个实现可用。

相对于AbstractSet只是重写了AbstractCollection的部分方法，SortedSet作为一个接口，提供了一部分新的方法用于支持排序及有序数据结构的查询。

```java
public interface SortedSet<E> extends Set<E> {
    }
```

# 主要方法
+ Comparator<? super E> comparator(); 返回与此有序集合关联的比较器，如果使用元素的自然顺序，则返回 null。
+ SortedSet<E> subSet(E fromElement, E toElement); 返回此有序集合的部分元素，元素范围从 fromElement（包括）到 toElement（不包括）。
+ SortedSet<E> headSet(E toElement); 用一个SortedSet, 返回此有序集合中小于end的所有元素。
+ SortedSet<E> tailSet(E fromElement); 返回此有序集合的部分元素，其元素大于或等于 fromElement。
+ E first(); 返回此有序集合中当前第一个（最小的）元素。
+ E last(); 返回此有序集合中最后一个（最大的）元素。
+ 迭代器
```java
default Spliterator<E> spliterator() {
        return new Spliterators.IteratorSpliterator<E>(
                this, Spliterator.DISTINCT | Spliterator.SORTED | Spliterator.ORDERED) {
            @Override
            public Comparator<? super E> getComparator() {
                return SortedSet.this.comparator();
            }
        };
    }
```