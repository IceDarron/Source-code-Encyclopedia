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
    
# 主要方法
## 构造函数
+ public LinkedList() {} 无参构造函数。
+ public LinkedList(Collection<? extends E> c) { this(); addAll(c); } 含参构造函数。调用addAll()方法。将c转换为数组后，遍历创建节点连接，修改   size和modCount。

## add系列方法
+ public boolean add(E e){} 调用linkLast(e)。
+ public void add(int index, E element){} 调用linkLast(element);或者linkBefore(element, node(index));。
+ public boolean addAll(Collection<? extends E> c){} 调用addAll(size, c)。
+ public boolean addAll(int index, Collection<? extends E> c){} 将c转换为数组后遍历创建节点连接。修改size和modCount。
+ public void addFirst(E e){} 调用linkFirst(e);。
+ public void addLast(E e){} 调用linkLast(e);。

这些方法，除了public boolean addAll(int index, Collection<? extends E> c)是含有实际操作逻辑外，其他函数均为对外暴露方法，其内部主要是调用LinkedList内的link系列方法。

## 索引检验方法
+ checkElementIndex和isElementIndex。说明参数是否为现有元素的索引。调用的方法get，set，remove。注意index与size的大小关系。
```java
private void checkElementIndex(int index) {
        if (!isElementIndex(index))
            throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    }
/**
 * Tells if the argument is the index of an existing element.
 */
private boolean isElementIndex(int index) {
        return index >= 0 && index < size; 这块区域是左闭右开。
    }
```

+ checkPositionIndex和isPositionIndex。说明参数是否是迭代器或添加操作的有效位置的索引。调用的方法add，addAll，listIterator。注意index与size的大小关系。
```java
private void checkPositionIndex(int index) {
        if (!isPositionIndex(index))
            throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    }
/**
 * Tells if the argument is the index of a valid position for an
 * iterator or an add operation.
 */
private boolean isPositionIndex(int index) {
        return index >= 0 && index <= size; // 这块区域是左闭右闭。
    }
```


## 迭代器
+ public Iterator<E> descendingIterator() { return new DescendingIterator(); } 创建降序迭代器。
+ public ListIterator<E> listIterator(int index) { checkPositionIndex(index); return new ListItr(index); } 创建内部类实现的迭代器。
    
## 插入方法
+ linkBefore 在一个不空的succ元素前插入e元素。
```java
/**
 * Inserts element e before non-null Node succ.
 */
void linkBefore(E e, Node<E> succ) {
    // assert succ != null;
    final Node<E> pred = succ.prev;
    final Node<E> newNode = new Node<>(pred, e, succ);
    succ.prev = newNode;
    if (pred == null)
        first = newNode;
    else
        pred.next = newNode;
    size++;
    modCount++;
}
```

+ linkFirst e作为头元素。
```java
/**
 * Links e as first element.
 */
private void linkFirst(E e) {
    final Node<E> f = first;
    final Node<E> newNode = new Node<>(null, e, f);
    first = newNode;
    if (f == null)
        last = newNode;
    else
        f.prev = newNode;
    size++;
    modCount++;
}
```

+ linkLast e作为尾元素。
```java
/**
 * Links e as last element.
 */
void linkLast(E e) {
    final Node<E> l = last;
    final Node<E> newNode = new Node<>(l, e, null);
    last = newNode;
    if (l == null)
        first = newNode;
    else
        l.next = newNode;
    size++;
    modCount++;
}
```


## 其他常用方法
+ public void clear(){} 清空元素。
```java
public void clear() {
        // Clearing all of the links between nodes is "unnecessary", but:
        // - helps a generational GC if the discarded nodes inhabit
        //   more than one generation
        // - is sure to free memory even if there is a reachable Iterator
        for (Node<E> x = first; x != null; ) {
            Node<E> next = x.next;
            x.item = null;
            x.next = null;
            x.prev = null;
            x = next;
        }
        first = last = null;
        size = 0;
        modCount++;
    }
```

+ public Object clone(){} 浅复制，元素本身没有被复制。
+ public boolean contains(Object o) { return indexOf(o) != -1; } 判断是否包含指定元素。
+ public E element() { return getFirst(); } 获取头元素。
+ public E get(int index){} 获取指定位置元素。需要重点了解下该方法内调用的Node<E> node(int index)方法。
    
```java
public E get(int index) {
        checkElementIndex(index);
        return node(index).item;
    }
Node<E> node(int index) {
        // assert isElementIndex(index);

        if (index < (size >> 1)) { // 查询方向，从头部开始。
            Node<E> x = first;
            for (int i = 0; i < index; i++)
                x = x.next;
            return x;
        } else { // 查询方向，从尾部开始。
            Node<E> x = last;
            for (int i = size - 1; i > index; i--)
                x = x.prev;
            return x;
        }
    }
```

+ public E getFirst(){} 获取第一个元素。直接调用first属性。
+ public E getLast(){} 获取最后一个元素。直接调用last属性。
+ public int indexOf(Object o){} 获取指定元素的索引。对于指定元素是否为空分别走两种遍历比较逻辑。从头元素向后遍历。
+ public int lastIndexOf(Object o){} 获取指定元素的索引。对于指定元素是否为空分别走两种遍历比较逻辑。从尾元素向前遍历。
+ private String outOfBoundsMsg(int index) { return "Index: "+index+", Size: "+size; } 用于在本类检查到IndexOutOfBoundsException异常并抛出异常时，提供索引及集合大小信息。


# 主要内部类

