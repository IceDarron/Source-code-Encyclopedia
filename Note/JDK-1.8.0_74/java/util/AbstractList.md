AbstractList

# 基本概念
AbstractList是List的骨架实现。继承了AbstractCollection抽象类。实现了List接口。

唯一构造器(通常由子类构造函数隐式调用,因为protected修饰,且无参数,所以可以被子类使用)。

2个私有内部类：Itr，ListItr。Itr实现了Iterator接口；ListItr继承了Itr，且实现了ListIterator接口。

2个辅助类：SubList，RandomAccessSubList。其中SubList扩展了AbstractList；而RandomAccessSubList扩展了SubList，实现了接口RandomAccess（随机访问）。

从源码中翻译出来的该类描述：
```java
/** 
 * 此类提供了List接口的骨架实现(也称为:抽象接口),以最大限度地减少实现由“随机访问”数据存储（如数组）。
 * 所支持的接口所需的工作量。
 * 对于顺序访问数据(如链表),应该使用AbstractSequentialList类。
 *
 * 为了实现一个不可修改的list,程序员上仅仅需要扩展此类,并提供一个get()方法和size()方法的实现即可。
 *
 * 为了实现一个可以修改的类,程序员上必须额外覆盖set(int,Object),set(int,E)方法。
 * 如果list的大小是可变的,则程序员上应该额外覆盖add()方法,remove()方法。
 *
 * 根据接口规范中的建议,程序员一般应该提供一个无参构造器方法。
 *
 * 和其它抽象集合实现不同,程序员上不必提供给一个迭代器实现:迭代器和list迭代器;迭代器和list迭代器已经在此类
 * 中实现,在"random access"方法之上。
 *
 * 本类中非抽象方法都描述了其实现的细节.如果集合希望实现一个高性能的方法,有些方法需要被覆写。
 */
```

# 提供的主要方法
+ protected AbstractList(){} 构造函数，允许子类使用。
+ public boolean add(E e){} 添加元素，在尾部添加新的元素。调用的add(int index, E element)未实现。
+ public void add(int index, E element){} index位置插入元素。未实现。
+ public boolean addAll(int index, Collection<? extends E> c){} 从集合c中index位置开始，讲元素插入到当前集合中需要add()方法支持。
+ public void clear(){} 清空元素。调用了removeRange(int fromIndex, int toIndex)方法。
+ public boolean equals(Object o){} 重写equals方法。
+ abstract public E get(int index); 获取指定index位置元素
+ hashCode() 计算hashcode。

```java
public int hashCode() {
      int hashCode = 1;
      for (E e : this)
          hashCode = 31*hashCode + (e==null ? 0 : e.hashCode());
      return hashCode;
  }
```
+ indexOf 查找指定元素(从前到后查找)。

```java
public int indexOf(Object o) {
    ListIterator<E> it = listIterator();
    if (o==null) {
        while (it.hasNext())
            if (it.next()==null)
                return it.previousIndex();
    } else {
        while (it.hasNext())
            if (o.equals(it.next()))
                return it.previousIndex();
    }
    return -1;
}
```

+ public Iterator<E> iterator() { return new Itr(); } 获取迭代器。
                                                      这一实现是对iterator接口的直接实现,依赖于list中size(),get(),add(),remove()方法.
                                                      并发修改中,这一实现会抛出运行时异常,抛出异常的根据是modeCount域的值.
+ lastIndexOf 查找指定元素(从后向前查找)。
+ public ListIterator<E> listIterator() { return listIterator(0); } 返回listIterator。
+ listIterator 接口的直接实现.
  
```java
public ListIterator<E> listIterator(final int index) {
    rangeCheckForAdd(index);

    return new ListItr(index);
}
```

+ outOfBoundsMsg 输出信息。

```java
private String outOfBoundsMsg(int index) {
        return "Index: "+index+", Size: "+size();
    }
```

+ rangeCheckForAdd 下标检查。

```java
private void rangeCheckForAdd(int index) {
        if (index < 0 || index > size())
            throw new IndexOutOfBoundsException(outOfBoundsMsg(index));
    }
```

+ public E remove(int index) { throw new UnsupportedOperationException(); } 删除指定位置元素。需要子类实现。
+ protected void removeRange(int fromIndex, int toIndex){} 删除区间元素。使用了迭代器和remove方法。
+ public E set(int index, E element) { throw new UnsupportedOperationException(); } 修改指定位置元素。需要子类实现。
+ subList 获取子数组。

```java
public List<E> subList(int fromIndex, int toIndex) {
        return (this instanceof RandomAccess ?
                new RandomAccessSubList<>(this, fromIndex, toIndex) :
                new SubList<>(this, fromIndex, toIndex));
    }
```

+ protected transient int modCount = 0; 此列表已在结构上修改的次数。用于迭代器中的fast-fail校验。子类直接继承该属性并使用。

# 内部类和辅助类
AbstractList内部类：
+ private class Itr implements Iterator<E>{} 私有内部类，提供了迭代器的实现。
+ private class ListItr extends Itr implements ListIterator<E>{} 私有内部类，增加了迭代器的前驱遍历。
AbstractList辅助类：
+ class SubList<E> extends AbstractList<E>{} 辅助类，扩展List中的subList方法？
+ class RandomAccessSubList<E> extends SubList<E> implements RandomAccess{} 辅助类，扩展了SubList，实现了接口RandomAccess。
      
# 参考资料
http://blog.csdn.net/caoxiaohong1005/article/details/79525127
