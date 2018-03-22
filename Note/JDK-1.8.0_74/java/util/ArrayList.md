
# 基本概念
ArrayList是List接口的一个主要实现类。继承了AbstractList，实现了List，RandomAccess, Cloneable, java.io.Serializable。

```java
public class ArrayList<E> extends AbstractList<E>
        implements List<E>, RandomAccess, Cloneable, java.io.Serializable {
    }
```

ArrayList是一个容量动态扩张的集合。底层数据结构是数组，查询快，增删慢，线程不安全，效率高。

多线程环境下可以考虑用Collections.synchronizedList(List list)函数返回一个线程安全的ArrayList类。
或者使用concurrent并发包下的CopyOnWriteArrayList类。

# 主要成员变量
+ private static final int DEFAULT_CAPACITY = 10; 初始集合容量。
+ transient Object[] elementData; // non-private to simplify nested class access 应该是1.8以后不再是私有属性。为什么。。？
+ private int size; 集合元素数。

# 主要方法
## 构造函数
+ public ArrayList() 空集合。
```java
public ArrayList() {
        this.elementData = DEFAULTCAPACITY_EMPTY_ELEMENTDATA;
    }
```
+ public ArrayList(int initialCapacity) 指定大小。
```java
public ArrayList(int initialCapacity) {
        if (initialCapacity > 0) {
            this.elementData = new Object[initialCapacity];
        } else if (initialCapacity == 0) {
            this.elementData = EMPTY_ELEMENTDATA;
        } else {
            throw new IllegalArgumentException("Illegal Capacity: "+
                                               initialCapacity);
        }
    }
```
+ public ArrayList(Collection<? extends E> c) 指定元素。使用到的Arrays.copyOf()方法，里面调用的都是System.arraycopy()。
```java
public ArrayList(Collection<? extends E> c) {
        elementData = c.toArray();
        if ((size = elementData.length) != 0) {
            // c.toArray might (incorrectly) not return Object[] (see 6260652)
            if (elementData.getClass() != Object[].class)
                elementData = Arrays.copyOf(elementData, size, Object[].class);
        } else {
            // replace with empty array.
            this.elementData = EMPTY_ELEMENTDATA;
        }
    }
```

## 范围校验
+ rangeCheck 在set，get，remove方法调用时使用。由于每次数组扩容的时候，是按照1.5倍扩容，所以在使用set，get，remove方法时需要先校验传递过来的index大小。
  这就解释了，扩容时留有的空位置，为什么不会被调用到，但是还占用了一部分内存。
```java
private void rangeCheck(int index) {
        if (index >= size)
            throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    }
```
+ rangeCheckForAdd 在add，addall方法调用时使用。保证每次都在尾部增加。
```java
private void rangeCheckForAdd(int index) {
        if (index > size || index < 0)
            throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    }
```

## 扩容机制
+ grow() 扩容方法。
```java
private void grow(int minCapacity) {
        // overflow-conscious code
        int oldCapacity = elementData.length;
        int newCapacity = oldCapacity + (oldCapacity >> 1); // 每次扩大1.5倍。
        if (newCapacity - minCapacity < 0)
            newCapacity = minCapacity; // 扩容不足。
        if (newCapacity - MAX_ARRAY_SIZE > 0)
            newCapacity = hugeCapacity(minCapacity); // 扩容过大。
        // minCapacity is usually close to size, so this is a win:
        elementData = Arrays.copyOf(elementData, newCapacity);
    }
```
以add()方法为例，扩容步骤如下：
```java
public boolean add(E e) {
  ensureCapacityInternal(size + 1);  // Increments modCount!!
  elementData[size++] = e;
  return true;
  }
  
private void ensureCapacityInternal(int minCapacity) {
      if (elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA) {
          minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
      }

      ensureExplicitCapacity(minCapacity);
  }
  
private void ensureExplicitCapacity(int minCapacity) {
    modCount++;

    // overflow-conscious code
    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
}
```
需要注意，扩容的时候同样修改了modCount。

## 其他
+ remove()方法 主要需要关注的是这一行elementData[--size] = null; // clear to let GC do its work。细节！
```java
public E remove(int index) {
        rangeCheck(index);

        modCount++;
        E oldValue = elementData(index);

        int numMoved = size - index - 1;
        if (numMoved > 0)
            System.arraycopy(elementData, index+1, elementData, index,
                             numMoved);
        elementData[--size] = null; // clear to let GC do its work

        return oldValue;
    }
```

