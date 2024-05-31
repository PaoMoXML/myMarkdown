公司框架在950前

当你想查询进行数据库操作时

你需要：

1. `IService`接口写一个接口

2. `ServiceImpl`实现类写一段很固定的代码

   例如：

   ```java
   @Override
       public List<ProductModelControl> findListByParentguid(String parentguid) {
           return new ProductModelControlService().findListByParentguid(parentguid);
       }
   ```

3. `Service`中用`baseDao.xxx()`方法执行sql（可以发现`Service`中是没有任何注解的）



可以看见第二段中有个**`new`**

居然使用**`new`**，我们`Spring`框架下居然用**`new`**

## 为啥不直接让Spring管理呢？

经过我的作死实验，其实是可以的

开始时我直接在`Service`上加了`@Service`：

```java
@Service
public class CalService
{
    protected ICommonDao baseDao;
    
    public CalService() {
        baseDao = CommonDao.getInstance();
    }
}
```

### 开始时运行没有任何问题，但是运行一段时间后就会报错：

![image-20220531173057020](https://cdn.jsdelivr.net/gh/PaoMoXML/image@main/img/image-20220531173057020.png)

我解决了半天没有解决掉，然后我就想到了 为什么公司要用`new`

当使用`new`去获取对象时，会走**构造函数!** 构造函数中有

`public CalService() {
        baseDao = CommonDao.getInstance();
    }`

也就是所公司框架每次去请求数据库会获取一遍`baseDao `

然后改成这样，加一个`@Scope("prototype")`就行了

```java
@Service
@Scope("prototype")
public class CalService
{
    protected ICommonDao baseDao;
    
    public CalService() {
        baseDao = CommonDao.getInstance();
    }
}
```



