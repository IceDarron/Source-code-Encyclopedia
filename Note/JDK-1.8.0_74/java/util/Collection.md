![Image text](https://github.com/IceDarron/Source-code-Encyclopedia/blob/master/Image/jdk_collection.png)

![Image text](https://github.com/IceDarron/Source-code-Encyclopedia/blob/master/Image/jdk_collection_new.png)

# 基本概念
jdk中Collection.java中的一段原文，该类是集合的一个跟接口，主要继承该接口的有List，Set，Queue。

## Collection接口主要提供的方法

+ boolean add(E e); 增加元素
+ boolean addAll(Collection<? extends E> c); 增加指定集合的所有元素到这个集合中。
+ void clear(); 清除所有元素。
+ boolean contains(Object o); 判断该集合是否包含指定元素。
+ boolean containsAll(Collection<?> c); 如果此集合包含指定集合中的所有元素，则为真。
+ boolean equals(Object o); 判断集合与指定对象是否相等。
+ int hashCode(); 计算该集合的哈希值。
+ boolean isEmpty(); 判断集合是否为空。
+ Iterator<E> iterator(); 返回该集合的迭代器。本身不保证返回元素的顺序。
+ boolean remove(Object o); 删除集合指定元素。
+ boolean removeAll(Collection<?> c); 删除包含在指定集合中的所有集合的元素（可选操作）。返回后，此集合将不包含与指定集合相同的元素。
+ removeIf 删除满足给定条件的集合的所有元素。
```java
default boolean removeIf(Predicate<? super E> filter) {
      Objects.requireNonNull(filter);
      boolean removed = false;
      final Iterator<E> each = iterator();
      while (each.hasNext()) {
          if (filter.test(each.next())) {
              each.remove();
              removed = true;
          }
      }
      return removed;
  }
/**
 * Predicate：可以用于接口请求参数校验、判断新老数据是否有变化需要进行更新操作。
 * @FunctionalInterface标记在接口上，“函数式接口”是指仅仅只包含一个抽象方法的接口。
 * java.lang.Object中的方法不是抽象方法。
 * default不是抽象方法。
 * static不是抽象方法。
 */
```
+ boolean retainAll(Collection<?> c); 仅保留集合中包含在指定集合中的元素（可选操作）。
+ int size(); 集合包含的元素个数。如果超过Integer.MAX_VALUE，则返回Integer.MAX_VALUE。
+ Object[] toArray(); 返回包含此集合中所有元素的数组。
+ <T> T[] toArray(T[] a); 返回数组的运行时类型是指定数组的运行时类型。
+ default Spliterator<E> spliterator() { return Spliterators.spliterator(this, 0); } Spliterator（splitable iterator可分割迭代器）
                                                                                   接口是Java为了并行遍历数据源中的元素而设计的迭代器。
                                                                                   iterator顺序遍历。spliterator并行遍历。
+ default Stream<E> stream() { return StreamSupport.stream(spliterator(), false); } 一种流的处理方式。不使用可分割迭代器。单管道。
+ default Stream<E> parallelStream() { return StreamSupport.stream(spliterator(), true); } 一种流的处理方式。使用可分割迭代器。多管道。
  
  
## Iterable 
Collection接口继承了java.lang.Iterable接口，支持集合对象迭代。


