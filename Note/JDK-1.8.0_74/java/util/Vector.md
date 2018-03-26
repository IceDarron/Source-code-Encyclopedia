# 基本概念
Vector底层数据结构是数组，查询快，增删慢。使用synchronized修饰了所有集合元素操作的方法，所以线程安全，但效率低。目前几乎已经淘汰了这个集合。

Vector的继承关系与ArrayList的继承关系相同，功能上也是一致的。唯一的区别就是Vector线程安全，ArrayList线程不安全。

```java
public class Vector<E>
    extends AbstractList<E>
    implements List<E>, RandomAccess, Cloneable, java.io.Serializable {
    }
```

# 主要属性
+ protected int capacityIncrement; 每次扩容的增量。默认无参构造器，该参数为0，则每次扩容时，容量翻倍。
+ protected int elementCount; 就是size。默认大小10。

# 主要方法
这里主要讲下扩容和synchronized。
+ synchronized method Vector类里的方法为了保证线程安全，受多线程影响的方法都加有synchronized。

+ grow 扩容方法。值得注意的是每次扩容的大小。如果创建Vector是没有指定capacityIncrement大小，那么构造器中会默认为0。这样每次扩容的大小就为2倍。
```java
private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + ((capacityIncrement > 0) ?
                                         capacityIncrement : oldCapacity);
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity;
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity);
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
```