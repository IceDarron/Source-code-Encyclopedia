# 基本概念
HashSet继承了AbstractSet，实现Set,Cloneable,java.in.Serializable接口。是一个无序元素不重复集合。集合元素可以为 NULL。

由源码构造函数可见，其底层实现是HashMap，主要使用的就是HashMap的Key，value值为一个Object对象。

对于hashCode和equals方法，如果equals比较相等，则hashCode必须相等。如果hashCode相等，equals可以不等。

判断两个对象是否相等，先判断hashCode是否相等，再判断equals是否相等。


```java
public class HashSet<E>
    extends AbstractSet<E>
    implements Set<E>, Cloneable, java.io.Serializable {

        //...

        public HashSet() {
                map = new HashMap<>();
            }

         //...
    }
```

# 主要属性
+ private transient HashMap<E,Object> map; 存放元素的HashMap。
+ private static final Object PRESENT = new Object(); // Dummy value to associate with an Object in the backing Map 即H按时Map的value值。


# 主要方法
基本上所有方法均为直接调用HashMap现有的方法。