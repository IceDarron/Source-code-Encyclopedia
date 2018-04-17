# 基本概念

```java
public class LinkedHashMap<K,V>
    extends HashMap<K,V>
    implements Map<K,V> {
    //...
}
```
LinkedHashMap 是一个关联数组、哈希表，它是线程不安全的，允许key为null,value为null。 
它继承自HashMap，实现了Map<K,V>接口。其内部还维护了一个双向链表，在每次插入数据，或者访问、修改数据时，会增加节点、或调整链表的节点顺序。以决定迭代时输出的顺序。

默认情况，遍历时的顺序是按照插入节点的顺序。这也是其与HashMap最大的区别。 


LinkedHashMap相对于HashMap的源码比，是很简单的。因为大树底下好乘凉。它继承了HashMap，仅重写了几个方法，以改变它迭代遍历时的顺序。这也是其与HashMap相比最大的不同。 
在每次插入数据，或者访问、修改数据时，会增加节点、或调整链表的节点顺序。以决定迭代时输出的顺序。

+ accessOrder ,默认是false，则迭代时输出的顺序是插入节点的顺序。若为true，则输出的顺序是按照访问节点的顺序。为true时，可以在这基础之上构建一个LruCache.
+ LinkedHashMap并没有重写任何put方法。但是其重写了构建新节点的newNode()方法.在每次构建新节点时，将新节点链接在内部双向链表的尾部
+ accessOrder=true的模式下,在afterNodeAccess()函数中，会将当前被访问到的节点e，移动至内部的双向链表的尾部。值得注意的是，afterNodeAccess()函数中，会修改modCount,因此当你正在accessOrder=true的模式下,迭代LinkedHashMap时，如果同时查询访问数据，也会导致fail-fast，因为迭代的顺序已经改变。
+ nextNode() 就是迭代器里的next()方法 。 
该方法的实现可以看出，迭代LinkedHashMap，就是从内部维护的双链表的表头开始循环输出。 
而双链表节点的顺序在LinkedHashMap的增、删、改、查时都会更新。以满足按照插入顺序输出，还是访问顺序输出。
+ 它与HashMap比，还有一个小小的优化，重写了containsValue()方法，直接遍历内部链表去比对value值是否相等。


# 参考资料
https://blog.csdn.net/zxt0601/article/details/77429150