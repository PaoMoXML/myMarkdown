### 配置文件配置

#### `DXP`

1. `jdbc`，建议只改下`ip`、`port`、`库名`其余都不动

2. `application.properties`

   1. `epoint.job.executor.appname`随便取一个名

   2. `epoint.job.executor.ip`当前服务器ip

   3. `epoint.job.executor.port`填入一个未被占用的端口

   4. `epoint.job.accessToken`随便填

      ![image-20220616091014195](https://xmls-typora-pic.oss-cn-shanghai.aliyuncs.com/pic/image-20220616091014195.png)

   5. `epoint.job.registry.addresses`配置统一调度地址

   	 

3. `epointframe.properties`

​		`epoint.job.registry.addresses`配置统一调度地址

4. `application.properties`配置`epoint.job.registry.addresses`

#### 统一调度

`application.properties`

1. 按照文件中说明配置

### 页面配置

1. 进入**节点域管理**->**新增节点域**->节点类型设置为**共享节点**

![image-20220616101940632](https://xmls-typora-pic.oss-cn-shanghai.aliyuncs.com/pic/image-20220616101940632.png)

2. ->保存后关闭，进入该节点的**配置服务**->**新增服务**根据下图配置并保存

<img src="https://xmls-typora-pic.oss-cn-shanghai.aliyuncs.com/pic/image-20220616102553167.png" alt="image-20220616102553167"  />

3. 进入**数据开发**->**导入流程**

![image-20220616103047122](https://xmls-typora-pic.oss-cn-shanghai.aliyuncs.com/pic/image-20220616103047122.png)

4. 配置数据源
   1. 导入流程会同步导入对应数据库
   1. 修改对应数据库配置
   1. 修改数据库后需要将**数据开发**->**流程**->**转换管理**->进入每一个转换**进行重新保存的发布**
5. 配置统一调度
   1. <img src="https://xmls-typora-pic.oss-cn-shanghai.aliyuncs.com/pic/image-20220616110234139.png" alt="image-20220616110234139"  />
   2. 检查执行器中是否有我们配置的节点
6. 合表配置
   1. 进入**房普流程一**->找到**1_数据分市合并及结构类型处理**->**定义表JSON**->**修改json**，json的key是合表后的表名，value是被合的表，表之间用“;”隔开
   1. 修改任何流程都需要**保存**并**发布**
7. 执行流程
   1. 进入流程的**流程开发**，点击运行
8. 正式执行
   1. 进入任务运维，选择流程，点击启动



