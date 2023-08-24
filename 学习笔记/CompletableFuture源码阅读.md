### 1. 以如下代码为例运行

```java
CompletableFuture.runAsync(() -> System.err.println("step1")).thenRun(() -> System.err.println("step2"));
```

#### 1.1 `runAsync`

```java
public class CompletableFuture<T> implements Future<T>, CompletionStage<T> {
    //执行线程池
    private static final Executor asyncPool = useCommonPool ?
        ForkJoinPool.commonPool() : new ThreadPerTaskExecutor();
    
    
    public static CompletableFuture<Void> runAsync(Runnable runnable) {
        return asyncRunStage(asyncPool, runnable);
    }
    
    /**
     * @param Executor 执行器
     * @param Runnable 可运行内容
     */
    static CompletableFuture<Void> asyncRunStage(Executor e, Runnable f) {
        if (f == null) throw new NullPointerException();
        //创建了一个新的CompletableFuture，并且返回值是Void
        CompletableFuture<Void> d = new CompletableFuture<Void>();
        //创建一个新的AsyncRun，将刚刚创建的CompletableFuture和Runnable传入
        //丢进线程中异步执行
        e.execute(new AsyncRun(d, f));
        return d;
    }
```

1. 创建新的`CompletableFuture`
2. 创建`AsyncRun`
3. 将`AsyncRun`丢入线程中执行

#### 1.2 `AsyncRun`

```java

    
    @SuppressWarnings("serial")
    static final class AsyncRun extends ForkJoinTask<Void>
            implements Runnable, AsynchronousCompletionTask {
        CompletableFuture<Void> dep;Runnable fn;
        //构造
        AsyncRun(CompletableFuture<Void> dep, Runnable fn) {
            this.dep = dep; this.fn = fn;
        }

        public final Void getRawResult() { return null; }
        public final void setRawResult(Void v) {}
        public final boolean exec() { run(); return true; }
		
        //丢入线程池后将会执行的方法
        public void run() {
            CompletableFuture<Void> d; Runnable f;
            //将d和f指向构造函数中的dep和fn
            if ((d = dep) != null && (f = fn) != null) {
                dep = null; fn = null;
                //d.result为空说明未执行
                if (d.result == null) {
                    try {
                        //运行
                        f.run();
                        //代码详见completeNull
                        d.completeNull();
                    } catch (Throwable ex) {
                        d.completeThrowable(ex);
                    }
                }
                d.postComplete();
            }
        }
    }
    
    static final AltResult NIL = new AltResult(null);
    //如果this中的RESULT参数为null，则将RESULT赋值为NIL
    final boolean completeNull() {
        return UNSAFE.compareAndSwapObject(this, RESULT, null,
                                           NIL);
    }
```

1. 运行业务逻辑-->`f.run()`
2. 给`RESULT`赋值
3. `d.postComplete();`

>tips:
>
>```java
>  public native boolean compareAndSwapObject(Object obj, long offset, Object expect, Object update);
>```
>
>- obj ：包含要修改的字段对象；
>- offset ：字段在对象内的偏移量；
>- expect ： 字段的期望值；
>- update ：如果该字段的值等于字段的期望值，用于更新字段的新值；

#### 1.3 `postComplete`

```java
final void postComplete() {
        /*
         * On each step, variable f holds current dependents to pop
         * and run.  It is extended along only one path at a time,
         * pushing others to avoid unbounded recursion.
         */
        CompletableFuture<?> f = this; Completion h;
        while ((h = f.stack) != null ||
               (f != this && (h = (f = this).stack) != null)) {
            CompletableFuture<?> d; Completion t;
            if (f.casStack(h, t = h.next)) {
                if (t != null) {
                    if (f != this) {
                        pushStack(h);
                        continue;
                    }
                    h.next = null;    // detach
                }
                f = (d = h.tryFire(NESTED)) == null ? this : d;
            }
        }
    }
    
}
```

[CompletableFuture运行流程源码详解 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/165030484)

[CompletableFuture源码解析 - Createsequence - 博客园 (cnblogs.com)](https://www.cnblogs.com/Createsequence/p/16963895.html)
