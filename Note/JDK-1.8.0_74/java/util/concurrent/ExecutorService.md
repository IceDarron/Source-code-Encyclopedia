### 基本概念
```java
public interface ExecutorService extends Executor {

    // 启动一个关闭命令，不再接受新任务
    // 当所有已提交任务执行完后，就关闭
    // 如果已经关闭，则调用没有其他作用
    void shutdown();

    // 试图停止所有正在执行的活动任务，暂停处理正在等待的任务
    // 并返回等待执行的任务列表
    List<Runnable> shutdownNow();

    boolean isShutdown();
    boolean isTerminated();

    // 提交任务
    // result应该是可以写一个固定的返回值
    <T> Future<T> submit(Callable<T> task);
    <T> Future<T> submit(Runnable task, T result); 
    Future<?> submit(Runnable task);

    // 批量提交任务
    <T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks)
        throws InterruptedException;

    <T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks,
                                  long timeout, TimeUnit unit)
        throws InterruptedException;

    <T> T invokeAny(Collection<? extends Callable<T>> tasks)
        throws InterruptedException, ExecutionException;

    <T> T invokeAny(Collection<? extends Callable<T>> tasks,
                    long timeout, TimeUnit unit)
        throws InterruptedException, ExecutionException, TimeoutException;
    
    // 该方法调用会被阻塞，直到所有任务执行完毕并且shutdown请求被调用，
    // 或者参数中定义的timeout时间到达或者当前线程被打断，
    // 这几种情况任意一个发生了就会导致该方法的执行。
    // 将shutdown的请求放在了awaitTermination之后，
    // 会导致只有awaitTermination方法执行完毕后才会执行shutdown请求，
    // 这样就造成了死锁。
    boolean awaitTermination(long timeout, TimeUnit unit)
            throws InterruptedException;
}
```

并发包下一个较为顶级的接口，继承Executor，扩展该接口提供了一些对线程操作的常用方法。
可以获取任务执行完的返回值。

