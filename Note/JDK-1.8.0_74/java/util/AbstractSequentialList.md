# 基本概念
AbstractSequentialList 继承自 AbstractList，是 LinkedList 的父类，是 List 接口 的简化版实现。

AbstractSequentialList 只支持按次序访问，而不像 AbstractList 那样支持随机访问。

AbstractSequentialList 把父类 AbstractList 中没有实现或者没有支持的操作都实现了，而且都是调用的 ListIterator 相关方法进行操作。

# 主要方法
+ 迭代器
```java
public Iterator<E> iterator() {
    //调用继承自
    return listIterator();
}

//继承 AbstractList 的 listIterator()
public ListIterator<E> listIterator() {
    return listIterator(0);
}

//需要实现类实现的方法
public abstract ListIterator<E> listIterator(int index);
```
+ public void add(int index, E element){} 在集合指定位置插入元素。
```java
public void add(int index, E element) {
        try {
            listIterator(index).add(element);
        } catch (NoSuchElementException exc) {
            throw new IndexOutOfBoundsException("Index: "+index);
        }
    }
```

+ public boolean addAll(int index, Collection<? extends E> c){} 在集合中指定位置插入所有指定集合的元素。
```java
public boolean addAll(int index, Collection<? extends E> c) {
        try {
            boolean modified = false;
            ListIterator<E> e1 = listIterator(index);
            Iterator<? extends E> e2 = c.iterator();
            while (e2.hasNext()) {
                e1.add(e2.next());
                modified = true;
            }
            return modified;
        } catch (NoSuchElementException exc) {
            throw new IndexOutOfBoundsException("Index: "+index);
        }
    }
```

+ public E get(int index){} 返回集合指定位置的元素。
```java
public E get(int index) {
        try {
            return listIterator(index).next();
        } catch (NoSuchElementException exc) {
            throw new IndexOutOfBoundsException("Index: "+index);
        }
    }
```

+ public E remove(int index){} 删除指定位置元素。
```java
public E remove(int index) {
        try {
            ListIterator<E> e = listIterator(index);
            E outCast = e.next();
            e.remove();
            return outCast;
        } catch (NoSuchElementException exc) {
            throw new IndexOutOfBoundsException("Index: "+index);
        }
    }
```

+ public E set(int index, E element){} 在集合指定位置替换成指定元素。
```java
public E set(int index, E element) {
        try {
            ListIterator<E> e = listIterator(index);
            E oldVal = e.next();
            e.set(element);
            return oldVal;
        } catch (NoSuchElementException exc) {
            throw new IndexOutOfBoundsException("Index: "+index);
        }
    }
```
