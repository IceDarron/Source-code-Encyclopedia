Queue 单向队列

# 基本概念
单向队列比较简单，只能向队尾添加元素，从队头删除元素。即一个先入先出（FIFO）队列的数据结构。

```java
public interface Queue<E> extends Collection<E> {
    }
```

# 主要方法
+ offer与add： 一些队列有大小限制，因此如果想在一个满的队列中加入一个新项，多出的项就会被拒绝。
  这时新的 offer 方法就可以起作用了。它不是对调用 add() 方法抛出一个 unchecked 异常，而只是得到由 offer() 返回的 false。
+ poll与remove： remove() 和 poll() 方法都是从队列中删除第一个元素（head）。
  当队列为空时，remove() 抛出异常NoSuchElementException。
                poll() 返回 null。
+ peek与element： element() 和 peek() 用于在队列的头部查询元素。
  当队列为空时， element() 抛出异常NoSuchElementException。
                 peek() 返回 null。


# 常见非阻塞队列
+ ArrayDeque（数组双端队列）
+ PriorityQueue（优先级队列）：类实质上维护了一个有序列表。加入到 Queue 中的元素根据它们的天然排序（通过其 java.util.Comparable 实现）或者根据传递给构造函数的 java.util.Comparator 实现来定位。
+ ConcurrentLinkedQueue（基于链表的并发队列）：是基于链接节点的、线程安全的队列。并发访问不需要同步。因为它在队列的尾部添加元素并从头部删除它们，所以只要不需要知道队列的大 小，ConcurrentLinkedQueue 对公共集合的共享访问就可以工作得很好。收集关于队列大小的信息会很慢，需要遍历队列。


# 常见阻塞队列
+ DelayQueue（延期阻塞队列）（阻塞队列实现了BlockingQueue接口）
+ ArrayBlockingQueue（基于数组的并发阻塞队列）
+ LinkedBlockingQueue（基于链表的FIFO阻塞队列）
+ LinkedBlockingDeque（基于链表的FIFO双端阻塞队列）
+ PriorityBlockingQueue（带优先级的无界阻塞队列）
+ SynchronousQueue（并发同步阻塞队列）

# 参考资料
https://blog.csdn.net/nimeijian/article/details/69258644







