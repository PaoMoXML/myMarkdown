### 依赖于Jedis

```xml
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
</dependency>
```



```java
@SpringBootConfiguration
@Scope("prototype")
public class RedisPool
{

    private Logger logger = LoggerFactory.getLogger(RedisPool.class);

    /**
     * redis连接池
     */
    private JedisPool pool;
    /**
     * redis连接
     */
    private String redisUrl = BaseConfigUtil.getConfigValue("epointframe","redisSetting");

    public RedisPool() {
        URI uri = URI.create(redisUrl);
        pool = new JedisPool(uri);
    }

    /**
     * 获取连接
     *
     * @return Jedis
     */
    public Jedis getJedis() {
        try {
            return pool.getResource();
        }
        catch (JedisConnectionException e) {
            logger.warn("jedis connection failed, try reconnecting.");
            try {
                TimeUnit.SECONDS.sleep(2L);
            }
            catch (InterruptedException ex) {
                ex.printStackTrace();
                Thread.currentThread().interrupt();
            }
        }
        catch (Exception e) {
            logger.error("连接出错，url：[{}]", redisUrl);
        }
        return null;
    }
```