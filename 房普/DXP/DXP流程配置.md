### 进入配置页面

进入`数据开发`->`ETL开发`->`流程开发`->`转换管理` 其中转换和汇交步骤一一对应

![image-20220621160715208](https://xmls-typora-pic.oss-cn-shanghai.aliyuncs.com/pic/image-20220621160715208.png)

---

**提示：**

1. **修改流程或转换后，都需要执行保存和发布操作**

![image-20220621162840520](https://xmls-typora-pic.oss-cn-shanghai.aliyuncs.com/pic/image-20220621162840520.png)

2. **如果修改了数据源，所有的转换都需要重新执行保存和发布**

3. 为防止流程出错后重复执行，需要在数据库中增加表`steprecord`以下为创建表的语句

   ```sql
   create table steprecord
   (
       step   integer,
       status integer
   );
   ```

   可在该表中查看执行了哪些步骤

---



### 1_数据分市合并及结构类型处理

1. 进入`定义表JSON`![image-20220621161532998](https://xmls-typora-pic.oss-cn-shanghai.aliyuncs.com/pic/image-20220621161532998.png)

2. 修改其中`JSON`，该`JSON`的`key`意为合表后的名称，`value`意为待合并的表，待合并的表用`;`隔开![image-20220621161634019](https://xmls-typora-pic.oss-cn-shanghai.aliyuncs.com/pic/image-20220621161634019.png)

3. 进入`执行结构类型_全国to试点Sql`，将对应`sql`复制进去

   ![image-20220621162354981](https://xmls-typora-pic.oss-cn-shanghai.aliyuncs.com/pic/image-20220621162354981.png)

### 2_建立汇交前置表

1. 进入`执行建立汇交前置表SQL`，操作方法同上一步的第三点，将对应`sql`复制进去

### 3_问题数据处理

1. 进入`问题处理sql`，方法同上

###  4_建立汇交中间表

1. 进入`建立备份表`，方法同上
2. 进入`调用DB存储过程`，选择对应**数据库**，选择对应`存储过程`

### 5_计算前更新市政原抗震设防值

1. 进入`执行更新市政原抗震设防值SQL`，方法同`3_问题数据处理`

### 8_计算后更新抗震设防

1. 进入`执行更新抗震设防SQL`，方法同上

### 9_基于全国库创建汇交表

1. 进入`基于全国库创建汇交表`，方法同上

### 10_更新汇交表的抗震设防字典

1. 进入`更新汇交表的抗震设防字典，方法同上`