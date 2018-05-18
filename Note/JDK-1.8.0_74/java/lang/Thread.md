### 基本概念
```java
public
class Thread implements Runnable {
    //...
}
```
实现了Runnable接口，通过继承该类可以实现多线程。

### 线程的概念
#### 线程状态：
>1) 新建（new）：线程被创建后的初始状态 
>2) 就绪（runnable）：调用start()方法，进入“就绪”状态 
>3) 运行（running）：进入“就绪”状态后，获取到cpu分配时间进入“运行状态” 
>4) 阻塞／等待（blocked／*waiting）:
>+ a.线程需要等待某个条件时，而条件未成熟时，等待调度器通知，进入‘等待’状态 
>+ b.获取某个资源时，资源被加锁，等待资源被释放，进入‘阻塞’状态 
>5) 死亡（terminated）：run()执行完毕或因异常导致退出，线程死亡

#### 线程重要属性：
+ 优先级
>在java中，每个线程都会拥有一个默认的优先级(5),如果未定义，则会继承父类的优先级，也通过setPriority(int)设置（1-10之间）。 
注意：优先级高的并不一定会先执行，cpu尽量将资源分配给优先级高的，即优先级高的执行几率要大很多，但并不是等优先级高执行完后才开始执行优先级低的，具有一定的随机性。

+ 守护线程
>在java中线程一般分为用户线程user和守护线程Daemon。它们在其他方面基本相同，但唯一的区别在于虚拟器中只剩下守护线程时，就可以退出了。 
守护线程是一种比较低级别的线程，一般用于为其他类别线程提供服务，因此当其他线程都退出时，它也就没有存在的必要了，例如jvm中的垃圾回收线程。可以通过setDaemon(boolean) 设置线程的Daemon模式。


### 主要方法
+ public synchronized void start() {} 线程对象被创建后，通过start方法启动，此时线程会被放置到等待队列中且状态由‘新建’进入‘就绪’，等待调度器分配资源，随时进入到‘运行’状态。
+ public void run() {if (target != null) {target.run();}} 线程被启动后，每当获取到资源，jvm就会去调用run方法，run执行结束，则线程结束。run只是一个普通的方法，存放业务逻辑。
+ wait - notify - notifyAll方法：这三个方法为java.lang.Object类为线程提供的用于实现线程间通信的同步控制方法。因此，可以说每一个对象实例都存在一个等待队列。
+ public final synchronized void join(long millis, int nanos) throws InterruptedException {}join方法本质上还是利用wait的来实现，t.join()即为当前主线程释放了t对象的monitor，进入到t对象阻塞队列中，巧妙的将t线程的执行达到了同步执行的效果。
+ park - parkNanons - unpark方法:LockSupport是JDK中比较底层的类，用来创建锁和其他同步工具类的基本线程阻塞原语。AQS也是通过LockSupport.park()和LockSupport.unpark(thread)来阻塞和唤醒的。
+ interrupt方法：一般来说，当线程调用thread.sleep()、obj.wait()、thread.join()处于阻塞状态时，此时再调用thread.interrupt(), thread会立即抛出InterruptedException异常，并清除“interrupt”状态。 而对于locksuport.park()方法来说，它会响应thread.interrupt()方法提前退出阻塞，但不会抛出InterruptedException异常及清除“interrupt”状态,使用者可自行对“interrupt”状态进行处理。 
+ public synchronized void start() {} 执行当前方法后，当前线程暂停指定毫秒数，进入‘等待’状态，这段时间内cpu不会再执行它。注意的是，它并不会交出互斥锁。
+ public static native void yield(); 执行当前方法后，线程从‘运行’状态变为‘就绪’状态，同时立马和其他线程一起竞争CPU，有可能还是它自身继续执行。当然它也不会交出互斥锁。

注意：wait()会立刻释放synchronized（obj）中的obj锁，以便其他线程可以执行obj.notify()。但是notify()不会立刻立刻释放sycronized（obj）中的obj锁，必须要等notify()所在线程执行完synchronized（obj）块中的所有代码才会释放这把锁。yield(),sleep()不会释放锁

### 参考资料
https://blog.csdn.net/lh513828570/article/details/60144598

