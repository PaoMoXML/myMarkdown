### 常用地址

> http://ip:poart/dxpmanager/frame/pages/epointjob/epointjoblist
> http://ip:poart/dxpmanager/frame/pages/epointjob/epointjobexecutorlist

### 问题

#### 1.执行器地址为空

1. 检查**统一调度参数配置**是否配置了正确的*调度服务地址*
2. `dxp`中**节点域管理->服务配置**中是否配置了正确的*节点服务`ip`*



#### 2.在画布中直接执行流程但是不输出日志

1. 确认是否将`dxpmanager`接入了`nginx`，如果接入了则在配置文件中确认是否有如下配置

   ```shell
   upstream dxpmanager {
           server ip:port;
       }
   location /dxpmanager {
           proxy_pass  http://dxpmanager;
       }
   location  /dxpmanager/websocket/pushlog {
   	proxy_pass http://dxpmanager;
       proxy_set_header Host $host:$server_port;
       proxy_http_version 1.1;
       proxy_set_header Upgrade $http_upgrade;
       proxy_set_header Connection "upgrade";	
   	}
   ```



#### 3.运行流程后提示无法连接到数据库

1. 确认数据源是否已经修改
2. 确认修改完数据源后是否刷新缓存



#### 4.某一流程长时间处于运行中

1. 可能是锁表了，请运行以下`sql`解锁表

```postgresql
select
 T.PID,
 T.STATE,
 T.QUERY,
 T.WAIT_EVENT_TYPE,
 T.WAIT_EVENT,
 T.QUERY_START
from
 PG_STAT_ACTIVITY T
where
 T.DATNAME = '需要解锁的表名';
 
 SELECT pg_cancel_backend(上面sql查询出的pid);
 
 
-- 查看所有正在运行的sql
SELECT 
procpid, 
start, 
now() - start AS lap, 
current_query 
FROM 
(SELECT 
backendid, 
pg_stat_get_backend_pid(S.backendid) AS procpid, 
pg_stat_get_backend_activity_start(S.backendid) AS start, 
pg_stat_get_backend_activity(S.backendid) AS current_query 
FROM 
(SELECT pg_stat_get_backend_idset() AS backendid) AS S 
) AS S 
WHERE 
current_query <> '<IDLE>'
ORDER BY 
lap DESC 
```



#### 5.dxp日志中提示`sql`运行报错，则以正常`sql`报错方式处理即可，一般是数据问题



#### 6. 流程中提示接口无法访问

1. 检查配置项中提到的四个接口地址修改是否操作



#### 7.流程无法运行，可先检查配置文件配置是否正确

1. 房普`dxp`的配置文件：`epointframe.properties`中`epoint.job.registry.addresses`是否配置了统一调度地址
2. 房普统一调度的配置文件：`application.properties`中`epoint.job.scheduler.callback.url`是否需要修改`ip`



#### 8.保存合表json后运行汇交流程查看日志发现合表json仍然是上次处理的地区

1. 根据`dxpmanager`的数据源中配置的业务库地址检查房普业务数据库（`zjb_fp`）中`mergejson`表中是否有最新添加的数据，如果没有请检查是否连接了正确的业务库



#### 9.汇交流程运行的很快，而且没有表生成

1. 检查相关日志中是否出现`空步骤`等字眼
2. 确认调查库`steprecord`表是否在执行步骤前已经清空



#### 10.执行汇交步骤二时报错提示缺少表

1. 检查步骤一中的日志，即使提示Success也需要将日志下载下来后确认
2. 如果没有报错，请参照问题**9**检查日志
3. 如果以上都没有问题，请确认汇交步骤是否为最新版
