### 工具类

```java
public class TaskLockUtil
{
    private final ReentrantLock reentrantLock;
    private final Condition condition;
    /**
     * 最大任务运行数量
     */
    private int maxTaskCount;
    /**
     * 正在运行的任务数量
     */
    private final AtomicInteger runningTaskCount;

    public TaskLockUtil(int maxTaskCount, AtomicInteger runningTaskCount) {
        this.maxTaskCount = maxTaskCount;
        this.runningTaskCount = runningTaskCount;
        // 公平的
        this.reentrantLock = new ReentrantLock(true);
        this.condition = this.reentrantLock.newCondition();
    }

    /**
     * [唤醒线程]
     *
     * @author xml
     * @date [2023/4/10]
     */
    public void signal() {
        final ReentrantLock reentrantLock = this.reentrantLock;
        try {
            reentrantLock.lockInterruptibly();
            //释放一个正在运行的任务，并判断是否可以唤醒
            if (runningTaskCount.decrementAndGet() < maxTaskCount) {
                condition.signal();
            }
        }
        catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
        finally {
            reentrantLock.unlock();
        }
    }

    /**
     * [阻塞线程]
     *
     * @author xml
     * @date [2023/4/10]
     */
    public void await() {
        final ReentrantLock reentrantLock = this.reentrantLock;
        try {
            reentrantLock.lockInterruptibly();
            //增加一个正在运行的任务，并判断是否需要阻塞
            if (runningTaskCount.incrementAndGet() >= maxTaskCount) {
                condition.await(600, TimeUnit.SECONDS);
            }
        }
        catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
        finally {
            reentrantLock.unlock();
        }
    }

    public AtomicInteger getRunningTaskCount() {
        return runningTaskCount;
    }

    public void thenRun() {
        final ReentrantLock reentrantLock = this.reentrantLock;
        try {
            reentrantLock.lockInterruptibly();
            int i = runningTaskCount.get();
            //判断是否有正在运行的任务，如果没有就往下执行，如果有就一直阻塞
            if (i == 0) {
                condition.signal();
            }
            else {
                condition.await(600, TimeUnit.SECONDS);
                thenRun();
            }
        }
        catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
        finally {
            reentrantLock.unlock();
        }
    }

    public int getMaxTaskCount() {
        return maxTaskCount;
    }

    public void setMaxTaskCount(int maxTaskCount) {
        this.maxTaskCount = maxTaskCount;
    }
}
```