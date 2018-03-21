![Image text](https://github.com/IceDarron/Source-code-Encyclopedia/blob/master/Image/jdk_collection.png)


> The root interface in the collection hierarchy. A collection represents a group of objects, known as its elements. 
Some collections allow duplicate elements and others do not. Some are ordered and others unordered. 
The JDK does not provide any direct implementations of this interface: 
it provides implementations of more specific subinterfaces like Set and List. 
This interface is typically used to pass collections around and manipulate them where maximum generality is desired.

jdk中Collection.java中的一段原文，该类是集合的一个跟接口，主要继承该接口的有List，Set，Queue。

Collection接口主要提供了以下方法：

+ boolean add(E e); 增加元素
+ boolean addAll(Collection<? extends E> c); 增加指定集合的所有元素到这个集合中。
+ void clear(); 清除所有元素。
+ boolean contains(Object o); 判断该集合是否包含指定元素。
+ boolean containsAll(Collection<?> c); 判断该集合是否包含指定集合的所有元素。
+ boolean equals(Object o); 判断集合与指定对象是否相等。
+ int hashCode(); 计算该集合的哈希值。
+ boolean isEmpty(); 判断集合是否为空。
+ Iterator<E> iterator(); 返回该集合的迭代器。本身不保证返回元素的顺序。
+ remove
+ removeAll
+ removeIf
+ retainAll
+ size
+ toArray
+ toArray

+ default Spliterator<E> spliterator() { return Spliterators.spliterator(this, 0); } Spliterator（splitable iterator可分割迭代器）
                                                                                   接口是Java为了并行遍历数据源中的元素而设计的迭代器。
                                                                                   iterator顺序遍历。spliterator并行遍历。
+ default Stream<E> stream() { return StreamSupport.stream(spliterator(), false); } 一种流的处理方式。单管道。
+ default Stream<E> parallelStream() { return StreamSupport.stream(spliterator(), true); } 一种流的处理方式。多管道。
