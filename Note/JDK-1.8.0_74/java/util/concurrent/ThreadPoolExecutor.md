### 基本概念
除了newScheduledThreadPool使用ScheduledThreadPoolExecutor，
newWorkStealingPool使用ForkJoinPool以外，
其余的工厂方法都使用了ThreadPoolExecutor类创建。

```java
public class ThreadPoolExecutor extends AbstractExecutorService {
    
    // 用一个Integer来存储线程池状态，和线程数
    // 其中高三位是状态，低29位是线程数
    private final AtomicInteger ctl = new AtomicInteger(ctlOf(RUNNING, 0));
    private static final int COUNT_BITS = Integer.SIZE - 3;
    private static final int CAPACITY   = (1 << COUNT_BITS) - 1;

    // 高三位111，接收新任务，处理阻塞队列任务
    private static final int RUNNING    = -1 << COUNT_BITS;
    // 高三位000，不接收新任务，处理阻塞队列任务
    private static final int SHUTDOWN   =  0 << COUNT_BITS;
    // 高三位001，不接收新任务，不处理阻塞队列任务， 而且中断运行中任务
    private static final int STOP       =  1 << COUNT_BITS;
    // 高三位010，所有线程均中断，woker数量为0
    private static final int TIDYING    =  2 << COUNT_BITS;
    // 高三位011，线程池中断完成
    private static final int TERMINATED =  3 << COUNT_BITS;

    // 计算ctl值，或者从ctl值中提取状态码或线程数
    private static int runStateOf(int c)     { return c & ~CAPACITY; }
    private static int workerCountOf(int c)  { return c & CAPACITY; }
    private static int ctlOf(int rs, int wc) { return rs | wc; }
    
    
    /**
     * （1）corePoolSize与maximumPoolSize
     *  线程池中的线程数，即使这部分线程空闲，也不会被销毁
     *  如果一个新任务提交，且创建的线程数少于corePoolSize，则新的线程被创建，即使已经创建的线程当中有空闲线程
     *  如果已经创建的线程大于corePoolSize，且小于maximumPoolSize，会先把任务提交到阻塞队列，在阻塞队列满的情况下，才会新建线程
     *  线程池中的线程数不可能大于maximumPoolSize
     * （2）keepAliveTime与unit
     *  keepAliveTime代表线程空闲时的存活时间，即当线程没有任务执行时，继续存活的时间；默认情况下，该参数只在线程数大于corePoolSize时才有用
     *  unit则为keepAliveTime的时间单位
     * （3）workQueue
     *  添加策略
     *  正在执行的线程小于corePoolSize，创建新线程
     *  正在执行的线程大于等于corePoolSize，把任务添加到阻塞队列
     *  阻塞队列满了，且正在执行的线程小于maximumPoolSize，创建新线程,否则拒绝任务
     *  阻塞队列类型
     *  ArrayBlockingQueue：基于数组结构的有界阻塞队列，按FIFO排序任务；
     *  LinkedBlockingQuene：基于链表结构的阻塞队列，按FIFO排序任务，吞吐量通常要高于ArrayBlockingQuene；
     *  SynchronousQuene：一个不存储元素的阻塞队列，每个插入操作必须等到另一个线程调用移除操作，否则插入操作一直处于阻塞状态，吞吐量通常要高于LinkedBlockingQuene；
     *  priorityBlockingQuene：具有优先级的无界阻塞队列；
     * （4）threadFactory
     *  创建线程的工厂，通过自定义的线程工厂可以给每个新建的线程设置一个具有识别度的线程名。
     * （5）handler
     *  线程池的饱和策略，当阻塞队列满了，且没有空闲的工作线程，如果继续提交任务，采取的处理策略。
     */
    public ThreadPoolExecutor(int corePoolSize,
                                  int maximumPoolSize,
                                  long keepAliveTime,
                                  TimeUnit unit,
                                  BlockingQueue<Runnable> workQueue,
                                  ThreadFactory threadFactory,
                                  RejectedExecutionHandler handler) {
            if (corePoolSize < 0 ||
                maximumPoolSize <= 0 ||
                maximumPoolSize < corePoolSize ||
                keepAliveTime < 0)
                throw new IllegalArgumentException();
            if (workQueue == null || threadFactory == null || handler == null)
                throw new NullPointerException();
            this.acc = System.getSecurityManager() == null ?
                    null :
                    AccessController.getContext();
            this.corePoolSize = corePoolSize;
            this.maximumPoolSize = maximumPoolSize;
            this.workQueue = workQueue;
            this.keepAliveTime = unit.toNanos(keepAliveTime);
            this.threadFactory = threadFactory;
            this.handler = handler;
        }
        
    // execute方法，要不就是新建一个worker执行任务，要不就将任务添加到阻塞队列中    
    public void execute(Runnable command) {
            if (command == null)
                throw new NullPointerException();
            /*
             * Proceed in 3 steps:
             *
             * 1. If fewer than corePoolSize threads are running, try to
             * start a new thread with the given command as its first
             * task.  The call to addWorker atomically checks runState and
             * workerCount, and so prevents false alarms that would add
             * threads when it shouldn't, by returning false.
             *
             * 2. If a task can be successfully queued, then we still need
             * to double-check whether we should have added a thread
             * (because existing ones died since last checking) or that
             * the pool shut down since entry into this method. So we
             * recheck state and if necessary roll back the enqueuing if
             * stopped, or start a new thread if there are none.
             *
             * 3. If we cannot queue task, then we try to add a new
             * thread.  If it fails, we know we are shut down or saturated
             * and so reject the task.
             */
            int c = ctl.get();
            if (workerCountOf(c) < corePoolSize) {
                if (addWorker(command, true))
                    return;
                c = ctl.get();
            }
            if (isRunning(c) && workQueue.offer(command)) {
                int recheck = ctl.get();
                if (! isRunning(recheck) && remove(command))
                    reject(command);
                else if (workerCountOf(recheck) == 0)
                    addWorker(null, false);
            }
            else if (!addWorker(command, false))
                reject(command);
        }
        
    private boolean addWorker(Runnable firstTask, boolean core) {
            retry:
            for (;;) {
                int c = ctl.get();
                int rs = runStateOf(c);
    
                // Check if queue empty only if necessary.
                if (rs >= SHUTDOWN &&
                    ! (rs == SHUTDOWN &&
                       firstTask == null &&
                       ! workQueue.isEmpty()))
                    return false;
    
                for (;;) {
                    int wc = workerCountOf(c);
                    if (wc >= CAPACITY ||
                        wc >= (core ? corePoolSize : maximumPoolSize))
                        return false;
                    if (compareAndIncrementWorkerCount(c))
                        break retry;
                    c = ctl.get();  // Re-read ctl
                    if (runStateOf(c) != rs)
                        continue retry;
                    // else CAS failed due to workerCount change; retry inner loop
                }
            }
    
            boolean workerStarted = false;
            boolean workerAdded = false;
            Worker w = null;
            try {
                w = new Worker(firstTask);
                final Thread t = w.thread;
                if (t != null) {
                    final ReentrantLock mainLock = this.mainLock;
                    mainLock.lock();
                    try {
                        // Recheck while holding lock.
                        // Back out on ThreadFactory failure or if
                        // shut down before lock acquired.
                        int rs = runStateOf(ctl.get());
    
                        if (rs < SHUTDOWN ||
                            (rs == SHUTDOWN && firstTask == null)) {
                            if (t.isAlive()) // precheck that t is startable
                                throw new IllegalThreadStateException();
                            workers.add(w);
                            int s = workers.size();
                            if (s > largestPoolSize)
                                largestPoolSize = s;
                            workerAdded = true;
                        }
                    } finally {
                        mainLock.unlock();
                    }
                    if (workerAdded) {
                        t.start();
                        workerStarted = true;
                    }
                }
            } finally {
                if (! workerStarted)
                    addWorkerFailed(w);
            }
            return workerStarted;
        }
        
    // Worker继承自AbstractQueuedSynchronizer同步器
    private final class Worker
            extends AbstractQueuedSynchronizer
            implements Runnable
        {
            /**
             * This class will never be serialized, but we provide a
             * serialVersionUID to suppress a javac warning.
             */
            private static final long serialVersionUID = 6138294804551838833L;
    
            /** Thread this worker is running in.  Null if factory fails. */
            final Thread thread;
            /** Initial task to run.  Possibly null. */
            Runnable firstTask;
            /** Per-thread task counter */
            volatile long completedTasks;
    
            /**
             * Creates with given first task and thread from ThreadFactory.
             * @param firstTask the first task (null if none)
             */
            Worker(Runnable firstTask) {
                setState(-1); // inhibit interrupts until runWorker
                this.firstTask = firstTask;
                this.thread = getThreadFactory().newThread(this);
            }
    
            /** Delegates main run loop to outer runWorker  */
            public void run() {
                runWorker(this);
            }
    
            // Lock methods
            //
            // The value 0 represents the unlocked state.
            // The value 1 represents the locked state.
    
            protected boolean isHeldExclusively() {
                return getState() != 0;
            }
    
            protected boolean tryAcquire(int unused) {
                if (compareAndSetState(0, 1)) {
                    setExclusiveOwnerThread(Thread.currentThread());
                    return true;
                }
                return false;
            }
    
            protected boolean tryRelease(int unused) {
                setExclusiveOwnerThread(null);
                setState(0);
                return true;
            }
    
            public void lock()        { acquire(1); }
            public boolean tryLock()  { return tryAcquire(1); }
            public void unlock()      { release(1); }
            public boolean isLocked() { return isHeldExclusively(); }
    
            void interruptIfStarted() {
                Thread t;
                if (getState() >= 0 && (t = thread) != null && !t.isInterrupted()) {
                    try {
                        t.interrupt();
                    } catch (SecurityException ignore) {
                    }
                }
            }
        }
        
    // 主要作用是不断重复的从阻塞队列中获取任务
    final void runWorker(Worker w) {
        Thread wt = Thread.currentThread();
        Runnable task = w.firstTask;
        w.firstTask = null;
        // 线程启动后，释放锁
        w.unlock(); // allow interrupts
        boolean completedAbruptly = true;
        try {
            // 调用getTask从阻塞队列获取任务
            while (task != null || (task = getTask()) != null) {
                // 任务执行前加锁
                w.lock();
                if ((runStateAtLeast(ctl.get(), STOP) ||
                     (Thread.interrupted() &&
                      runStateAtLeast(ctl.get(), STOP))) &&
                    !wt.isInterrupted())
                    wt.interrupt();
                try {
                    beforeExecute(wt, task);
                    Throwable thrown = null;
                    try {
                        // 开始执行任务
                        task.run();
                    } catch (RuntimeException x) {
                        thrown = x; throw x;
                    } catch (Error x) {
                        thrown = x; throw x;
                    } catch (Throwable x) {
                        thrown = x; throw new Error(x);
                    } finally {
                        afterExecute(task, thrown);
                    }
                } finally {
                    task = null;
                    w.completedTasks++;
                    w.unlock();
                }
            }
            completedAbruptly = false;
        } finally {
            processWorkerExit(w, completedAbruptly);
        }
    }

    // 从阻塞队列中获取任务
    private Runnable getTask() {
        boolean timedOut = false; // Did the last poll() time out?

        for (;;) {
            int c = ctl.get();
            int rs = runStateOf(c);

            // Check if queue empty only if necessary.
            if (rs >= SHUTDOWN && (rs >= STOP || workQueue.isEmpty())) {
                decrementWorkerCount();
                return null;
            }

            int wc = workerCountOf(c);

            // Are workers subject to culling?
            boolean timed = allowCoreThreadTimeOut || wc > corePoolSize;

            if ((wc > maximumPoolSize || (timed && timedOut))
                && (wc > 1 || workQueue.isEmpty())) {
                if (compareAndDecrementWorkerCount(c))
                    return null;
                continue;
            }

            try {
                // 主要实现
                Runnable r = timed ?
                    workQueue.poll(keepAliveTime, TimeUnit.NANOSECONDS) :
                    workQueue.take();
                if (r != null)
                    return r;
                timedOut = true;
            } catch (InterruptedException retry) {
                timedOut = false;
            }
        }
    }
}
```

![ThreadPoolExecutor模型](https://github.com/IceDarron/Source-code-Encyclopedia/blob/master/Image/jdk_ThreadPoolExecutor_work.png)

可以看到Worker继承了AQS抽象类并且实现了Runnable接口，其是ThreadPoolExecutor的核心内部类。
而对于AbortPolicy，用于被拒绝任务的处理程序，它将抛出 RejectedExecutionException。
CallerRunsPolicy，用于被拒绝任务的处理程序，它直接在 execute 方法的调用线程中运行被拒绝的任务，如果执行程序已关闭，则会丢弃该任务。
DiscardPolicy，用于被拒绝任务的处理程序，默认情况下它将丢弃被拒绝的任务。
DiscardOldestPolicy，用于被拒绝任务的处理程序，它放弃最旧的未处理请求，然后重试 execute；如果执行程序已关闭，则会丢弃该任务。
这些都是拒绝任务提交时的所采用的不同策略。

### 相关资料
https://blog.csdn.net/qq_22929803/article/details/52347381