# 基本概念
LinkedHashSet:HashSet的子类，底层采用了哈希表算法以及链表算法，既保证了元素的添加顺序，也保证了查询效率。但是整体性能要低于 HashSet。

LinkedHashSet的所有构造器都是直接调用HashSet的同一个构造器，返回的是一个LinkedHashMap（具体底层实现该出不讲述，了解是哈希表和双向链表集合）。

```java
public class LinkedHashSet<E>
    extends HashSet<E>
    implements Set<E>, Cloneable, java.io.Serializable {
        //...

        //以一个无参构造器为例，直接使用HashSet的构造函数。
        public LinkedHashSet() {
                super(16, .75f, true);
            }
        //...
    }


public class HashSet<E>
    extends AbstractSet<E>
    implements Set<E>, Cloneable, java.io.Serializable {

        //...

        //在HashSet中看，LinkedHashSet调用的构造函数在HashSet的实现，直接返回了LinkedHashMap。
        HashSet(int initialCapacity, float loadFactor, boolean dummy) {
                map = new LinkedHashMap<>(initialCapacity, loadFactor);
            }

        //...
    }
```

