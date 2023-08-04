### 代码

```java
public abstract class ConcurrentListAllData implements Runnable
{
    protected static final Logger log = LogUtil.getSLF4JLog(ConcurrentListAllData.class);
    protected static final String REDISSPLITKEY = "-";
    /**
     * 线程池
     */
    protected final ExecutorService poolInstance = BatchCodeSingletonInit.INSTANCE.getPoolInstance();
    /**
     * redis连接池
     */
    protected final RedisPool redisPool = SzjsContextUtil.getBean(RedisPool.class);
    protected final int pageSize;
    private final TaskLockUtil dataSupplierTaskLockUtil;
    private final TaskLockUtil dataConsumerTaskLockUtil;
    protected String redisKey;
    private final int maxTaskCount;

    protected ConcurrentListAllData(int maxTaskCount, String redisKey, int pageSize) {
        Assert.notBlank(redisKey, "入参redisKey不能为空");
        Assert.isTrue(pageSize > 0, "入参topageSize必须大于0");

        //这样操作后最小都会占用两个线程
        int consumerTaskCount = maxTaskCount / 3 > 0 ? maxTaskCount / 3 : 1;
        int supplierTaskCount = maxTaskCount - consumerTaskCount > 0 ? maxTaskCount - consumerTaskCount : 1;

        //正在运行的获取数据的任务数量
        this.dataSupplierTaskLockUtil = new TaskLockUtil(supplierTaskCount, new AtomicInteger());
        this.dataConsumerTaskLockUtil = new TaskLockUtil(consumerTaskCount, new AtomicInteger());
        this.maxTaskCount = maxTaskCount;
        this.pageSize = pageSize;
        this.redisKey = redisKey;
    }

    /**
     * 定义如何获取数据总量
     *
     * @return 数据总量
     */
    protected abstract int getDataTotalCount();

    /**
     * 定义如何分页获取数据
     *
     * @param index    页码
     * @param pageSize 页大小
     * @return 分页后的数据
     */
    protected abstract List<Record> pageListData(int index, int pageSize);

    /**
     * 数据获取完后的回调
     */
    protected abstract void pageListDataFinishCallback();

    /**
     * 数据获取失败的回调
     *
     * @param throwable
     */
    protected abstract void pageListDataFailCallback(Throwable throwable);

    /**
     * 数据提供
     *
     * @author xml
     * @date [2023/4/27]
     */
    private void dataSupplier() {
        int loopCount;
        try {
            EpointFrameDsManager.begin();
            loopCount = Convert.toInt(NumberUtil.div(getDataTotalCount(), pageSize, 0, RoundingMode.UP));
            EpointFrameDsManager.commit();
        }
        finally {
            EpointFrameDsManager.close();
        }

        //用于保存已经获取到的数据量
        AtomicInteger gettedReocrdCount = new AtomicInteger();

        CompletableFuture.runAsync(() -> {
            List<Future<String>> futureList = new ArrayList<>();
            for (int i = 0; i < loopCount; i++) {
                //增加一次循环次数
                final int nowLoopCount = i;
                Future<String> submit = poolInstance.submit(() -> {
                    try (Jedis jedis = redisPool.getJedis()) {
                        EpointFrameDsManager.begin();
                        log.info("[{}]-->获取第:[{}]组数据", redisKey, nowLoopCount);
                        List<Record> recordList = pageListData(nowLoopCount, pageSize);
                        if (CollUtil.isNotEmpty(recordList)) {
                            //加入到redis中
                            jedis.set(redisKey + REDISSPLITKEY + nowLoopCount, JSON.toJSONString(recordList));
                        }
                        log.info("[{}]-->共获取到了:[{}]条数据", redisKey,
                                gettedReocrdCount.addAndGet(recordList.size()));
                        EpointFrameDsManager.commit();
                    }
                    catch (Exception e) {
                        EpointFrameDsManager.rollback();
                        throw e;
                    }
                    finally {
                        EpointFrameDsManager.close();
                        //判断是否可以唤醒线程
                        dataSupplierTaskLockUtil.signal();
                    }
                    return "开始获取";
                });
                futureList.add(submit);
                //判断是否需要阻塞
                dataSupplierTaskLockUtil.await();
            }
            for (Future<String> stringFuture : futureList) {
                try {
                    stringFuture.get(EXPIRESECOND, TimeUnit.SECONDS);
                }
                catch (InterruptedException | ExecutionException | TimeoutException e) {
                    throw new RuntimeException(e);
                }
            }
        }).thenRun(() -> {
            dataSupplierTaskLockUtil.thenRun();
            //数据获取已经结束了，所以将消费的线程提升至满线程
            dataConsumerTaskLockUtil.setMaxTaskCount(maxTaskCount);
            dsManagerUtil(this::pageListDataFinishCallback).run();
        }).exceptionally(throwable -> {
            dsManagerUtil(() -> pageListDataFailCallback(throwable)).run();
            log.error(throwable.getMessage(), throwable);
            return null;
        });
    }

    /**
     * 定义如何消费数据
     *
     * @param array 待销费的数据
     */
    protected abstract void dataConsumer(JSONArray array);

    /**
     * 数据消费完后的回调
     */
    protected abstract void dataConsumeFinishCallback();

    /**
     * 诗句消费失败的回调
     *
     * @param throwable
     */
    protected abstract void dataConsumeFailCallback(Throwable throwable);

    /**
     * 数据消费
     *
     * @author xml
     * @date [2023/4/27]
     */
    private void dataConsumer() {
        int loopCount;
        try {
            EpointFrameDsManager.begin();
            loopCount = Convert.toInt(NumberUtil.div(getDataTotalCount(), pageSize, 0, RoundingMode.UP));
            EpointFrameDsManager.commit();
        }
        finally {
            EpointFrameDsManager.close();
        }
        CompletableFuture.runAsync(() -> {
            List<Future<String>> futureList = new ArrayList<>();
            for (int i = 0; i < loopCount; i++) {
                String thisLoopRedisKey = redisKey + REDISSPLITKEY + i;
                log.info("开始消费数据:[{}]", thisLoopRedisKey);
                Future<String> submit = poolInstance.submit(() -> {
                    try (Jedis jedis = redisPool.getJedis()) {
                        for (; ; ) {
                            if (Boolean.TRUE.equals(jedis.exists(thisLoopRedisKey))) {
                                try {
                                    //获取已经存储到redis的数据
                                    String jsonStringRecod = jedis.get(thisLoopRedisKey);
                                    dsManagerUtil(() -> dataConsumer(JSON.parseArray(jsonStringRecod))).run();
                                    jedis.del(thisLoopRedisKey);
                                    //判断是否可以唤醒线程
                                    break;
                                }
                                finally {
                                    dataConsumerTaskLockUtil.signal();
                                }
                            }
                        }
                    }
                    return "开始消费";
                });
                futureList.add(submit);
                dataConsumerTaskLockUtil.await();
            }
            for (Future<String> stringFuture : futureList) {
                try {
                    stringFuture.get(EXPIRESECOND, TimeUnit.SECONDS);
                }
                catch (InterruptedException | ExecutionException | TimeoutException e) {
                    throw new RuntimeException(e);
                }
            }
        }).thenRun(() -> {
            dataConsumerTaskLockUtil.thenRun();
            dsManagerUtil(this::dataConsumeFinishCallback).run();
        }).exceptionally(throwable -> {
            dsManagerUtil(() -> dataConsumeFailCallback(throwable)).run();
            log.error(throwable.getMessage(), throwable);
            return null;
        });
    }

    @Override
    public void run() {
        dataSupplier();
        dataConsumer();
    }

    private Runnable dsManagerUtil(Runnable runnable) {
        return () -> {
            try {
                EpointFrameDsManager.begin();
                runnable.run();
                EpointFrameDsManager.commit();
            }
            catch (Exception e) {
                EpointFrameDsManager.rollback();
                throw e;
            }
            finally {
                EpointFrameDsManager.close();
            }
        };
    }
}
```

### 使用说明

创建一个具体的功能类，继承这个抽象类，实现其中的方法

构造函数入参说明：

1. `maxTaskCount`：最大任务数量
2. `redisKey`：用于`redis`中的唯一标识
3. `pageSize`：页码大小

方法说明：

| 方法名                                               | 是否必须实现 | 说明                   |
| ---------------------------------------------------- | ------------ | ---------------------- |
| `int getDataTotalCount()`                            | √            | 定义如何获取数据总量   |
| `List<Record> pageListData(int index, int pageSize)` | √            | 分页获取数据           |
| `void pageListDataFinishCallback()`                  | --           | 所有数据获取完后的回调 |
| `void pageListDataFailCallback(Throwable throwable)` | --           | 数据获取失败的回调     |
| `void dataSupplier()`                                | ×            | **数据获取的具体实现** |
| `void dataConsumer(JSONArray array)`                 | --           | 定义如何消费数据       |
| `void dataConsumeFinishCallback()`                   | --           | 数据消费完成的回调     |
| `void dataConsumeFailCallback(Throwable throwable)`  | --           | 数据消费失败的回调     |
| `void dataConsumer()`                                | ×            | **数据消费的具体实现** |
|                                                      |              |                        |

