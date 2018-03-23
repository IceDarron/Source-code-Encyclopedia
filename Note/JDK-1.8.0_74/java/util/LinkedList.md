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
+ public E get(int index){}和Node<E> node(int index){} 获取指定位置元素。需要重点了解下该方法内调用的Node<E> node(int index)方法。
    
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
+ set() 修改指定位置为指定元素。
```java
public E set(int index, E element) {
        checkElementIndex(index);
        Node<E> x = node(index);
        E oldVal = x.item;
        x.item = element;
        return oldVal;
    }
```
+ public int size() { return size; } 返回集合大小
+ public Object[] toArray(){}和public <T> T[] toArray(T[] a){} 返回数组。


## Queue操作
Queue操作提供了peek()、element()、poll()、remove()、offer(E e)这些方法。

这些方法均是通过调用LinkedList现有的方法实现功能。

## Deque（双端队列）操作
Deque操作提供了offerFirst(E e)、offerLast(E e)、peekFirst()、peekLast()、pollFirst()、pollLast()、push(E e)、pop()、removeFirstOccurrence(Object o)、removeLastOccurrence(Object o)这些方法。

这些方法均是通过调用LinkedList现有的方法实现功能。

## remove系列方法
+ public E remove(){} 删除第一个元素。但实际上List接口并没有定义该方法。
+ public E remove(int index){} 删除指定索引位置的元素。List接口对外公布的。
+ public boolean remove(Object o){} 从头部位置遍历，删除指定的第一个元素。重复的不删除。
+ public E removeFirst(){} 删除第一个元素。
+ public boolean removeFirstOccurrence(Object o){} 从头部位置遍历，删除指定的第一个元素。重复的不删除。
+ public E removeLast(){} 删除最后一个元素。
+ public boolean removeLastOccurrence(Object o){} 从尾部位置遍历，删除指定的第一个元素。重复的不删除。

remove方法通过遍历或者直接获取node节点的方式删除方法。

## unlink系列方法
+ E unlink(Node<E> x){} 断开非空节点x的链接。
```java
E unlink(Node<E> x) {
    // assert x != null;
    final E element = x.item;
    final Node<E> next = x.next;
    final Node<E> prev = x.prev;

    if (prev == null) {
        first = next;
    } else {
        prev.next = next;
        x.prev = null;
    }

    if (next == null) {
        last = prev;
    } else {
        next.prev = prev;
        x.next = null;
    }

    x.item = null;
    size--;
    modCount++;
    return element;
}
```

+ private E unlinkFirst(Node<E> f){} 删除非空的首节点f。
```java
private E unlinkFirst(Node<E> f) {
        // assert f == first && f != null;
        final E element = f.item;
        final Node<E> next = f.next;
        f.item = null;
        f.next = null; // help GC
        first = next;
        if (next == null)
            last = null;
        else
            next.prev = null;
        size--;
        modCount++;
        return element;
    }
```
    
+ private E unlinkLast(Node<E> l){} 删除非空的尾节点l。
```java
private E unlinkLast(Node<E> l) {
        // assert l == last && l != null;
        final E element = l.item;
        final Node<E> prev = l.prev;
        l.item = null;
        l.prev = null; // help GC
        last = prev;
        if (prev == null)
            first = null;
        else
            prev.next = null;
        size--;
        modCount++;
        return element;
    }
```

remove系列方法实际上就是调用unlink系列方法，所以remove只是对外公布的集合删除操作方法，实际的删除链接都是unlink系列方法。

## 序列化的读写入函数
+ private void readObject(java.io.ObjectInputStream s){} 反序列化：先将LinkedList的“大小”读出，然后将“所有的元素值”读出。
```java
private void readObject(java.io.ObjectInputStream s)
        throws java.io.IOException, ClassNotFoundException {
        // Read in any hidden serialization magic
        s.defaultReadObject();

        // Read in size
        int size = s.readInt();

        // Read in all elements in the proper order.
        for (int i = 0; i < size; i++)
            linkLast((E)s.readObject());
    }
```
+ private void writeObject(java.io.ObjectOutputStream s){} 序列化：将linkedList的“大小，所有的元素值”都写入到输出流中。
```java
private void writeObject(java.io.ObjectOutputStream s)
        throws java.io.IOException {
        // Write out any hidden serialization magic
        s.defaultWriteObject();

        // Write out size
        s.writeInt(size);

        // Write out all elements in the proper order.
        for (Node<E> x = first; x != null; x = x.next)
            s.writeObject(x.item);
    }
```


# 主要内部类



# 参考资料
该资料从不同的角度归纳了LinkedList下的所有方法：https://www.cnblogs.com/CherishFX/p/4734490.html

https://www.cnblogs.com/ysocean/p/6555373.html

http://blog.csdn.net/pml18710973036/article/details/78452717
