LinkedList集合

# 基本概念

LinkedList继承AbstractSequentialList，实现List，Deque，Cloneable，java.io.Serializable接口。

LinkedList是一个双向链表，可以被当作堆栈、队列或双端队列进行操作。

线程不安全。

不支持随机查询，查询效率低于ArrayList。对于增加删除操作，由于只需要修改部分元素的指针，所以效率较高。

头结点中不存放数据。就是一个null。

```java
public class LinkedList<E>
    extends AbstractSequentialList<E>
    implements List<E>, Deque<E>, Cloneable, java.io.Serializable {
}
```

# 主要属性
+ transient int size = 0; 当前有多少个节点。  
+ transient Node<E> first; 第一个节点。 
+ transient Node<E> last; 最后一个节点。


