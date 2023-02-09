### 使用in的情况

如果是确定且有限的集合时，可以使用。如 IN （0，1，2）。
其余建议使用 inner join




### 使用占位符，并且可以动态添加参数的方法
```java
    public List<SzjsPersonbadbehavior> findListByBehaviortype(String nodeid) {
        String sql="select * from szjs_personbadbehavior where 1=1 ";
        List<Object> params=new ArrayList<>();
        if(StringUtil.isNotBlank(nodeid)) {
            sql+=" and behaviortype =?";
            params.add(nodeid);
        }
        //按照行为代码进行排序
        sql+=" order by behaviorvalue desc";
        return this.findList(sql, SzjsPersonbadbehavior.class, params.toArray());
    }
```

### sql中日期比较

```mysql
现在比较 某个时间 两个时间相差大于336天
where DATEDIFF(now(), 某个时间) > 336;
```
### 获取某一系列数据中最新的数据

```mysql
select sl.*
//通过max函数获取最新的时间，通过某系列唯一标识（liftguid）去分类（可加上查询条件）
from (select liftguid, MAX(detectiondate) as detectiondate
      from safe_liftdetection
      where status = '03'
      group by liftguid) a
      //再通过某系列唯一标识（liftguid）和日期 左连接自己确定唯一数据，最后对查询的最新数据进行条件判断
         left join safe_liftdetection sl on a.liftguid = sl.liftguid and a.detectiondate = sl.detectiondate
where DATEDIFF(now(), sl.detectiondate) > 336;
```
### 日期格式化
```mysql
-- 年-月-日 时:分:秒（2020-07-18 11:02:15）
select DATE_FORMAT(NOW(),'%Y-%m-%d %H:%i:%S');
 
-- 年-月-日，2020-07-18
select DATE_FORMAT(NOW(),'%Y-%m-%d');
 
-- 时:分:秒，11:02:58
select DATE_FORMAT(NOW(),'%H:%i:%S');
 
-- 返回星期名称，如：Saturday
select DATE_FORMAT(NOW(),'%W');
 
-- 返回月份名称和缩略名称，如：月份全名称：July 缩略名：Jul
select DATE_FORMAT(NOW(),'月份全名称：%M 缩略名：%b');
 
-- 返回今日是本年的第几天，如：今年已过去：200天
select DATE_FORMAT(NOW(), '今年已过去：%j天');
```

### join后跟where和and的区别
> 1. on条件是在生成临时表时使用的条件，它不管on中的条件是否为真，都会返回左边表中的记录。 on 后面跟and条件，先会在右边表中对and条件进行过滤，然后再跟左边主表进行关联。
>
> 2. where条件是在临时表生成好后，再对临时表进行过滤的条件。这时已经没有left join的含义（必须返回左边表的记录）了，条件不为真的就全部过滤掉。