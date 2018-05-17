### 基本概念
```java
public interface Executor {
    void execute(Runnable command);
}
```

定义了一个execute函数用来提交一个Runnable的任务。
该方式提交的任务不能获取返回值，因此无法判断任务是否执行成功。