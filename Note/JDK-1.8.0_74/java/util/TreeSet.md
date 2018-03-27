# 基本概念
不可重复，底层使用 红黑树算法，擅长于范围查询。

不保证元素的添加顺序，但是会对集合中的元素进行排序。

必须放入同样类的对象(默认会进行排序)，否则可能会发生类型转换异常。我们可以使用泛型来进行限制。

```java
public class TreeSet<E> extends AbstractSet<E>
    implements NavigableSet<E>, Cloneable, java.io.Serializable {
    }
```

底层是TreeMap。