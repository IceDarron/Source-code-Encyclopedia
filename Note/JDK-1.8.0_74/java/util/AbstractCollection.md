该抽象类实现了Collection最基本的实现（无关于各个具体集合实现类的数据结构或调用的迭代器在各个具体实现类实现）。

对于一个不可更改的集合，只要继承这个类并且实现迭代器和size()方法就行。

对于一个可更改的集合，还需要实现add和返回Iterator的方法，当然可选的实现remove方法。

# 主要方法
+ public abstract Iterator<E> iterator(); 迭代器的实现仍旧抽象，由子类实现。
+ public abstract int size(); 由子类实现。
+ public boolean contains(Object o){} 通过遍历迭代器实现。 
+ public Object[] toArray(){} 通过遍历迭代器实现。 
+ public boolean add(E e); 由子类重写实现。
+ public boolean remove(Object o){} 通过遍历迭代器实现。 
+ public boolean containsAll(Collection<?> c){} 调用contains()。
+ public boolean addAll(Collection<? extends E> c){} 调用add()。
+ public boolean removeAll(Collection<?> c){} 通过遍历迭代器及其remove()实现。 
+ public boolean retainAll(Collection<?> c){} 通过遍历迭代器及其remove()实现。 
+ public void clear(){} 通过遍历迭代器及其remove()实现。 
+ public String toString(){} 通过遍历迭代器实现。 

两个较为特殊且重要的方法。

+ private static <T> T[] finishToArray(T[] r, Iterator<?> it){}
+ private static int hugeCapacity(int minCapacity){}

# 主要成员变量
private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8; 可分配的最大数组大小。
