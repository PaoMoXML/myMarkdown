```mermaid
classDiagram
	  省市县基本信息 o-- 人口普查信息
      省市县基本信息 o-- 房屋基本信息
      房屋基本信息 o-- 房屋额外信息
      
```







1. 协助山东处理dxp和业务系统的问题
2. dxp上数据处理流程支持多个dxp系统对应一个业务系统
3. 全文检索深度查询功能支持



1. 房普房屋建筑页面和项目组上功能同步
2. 全文检索指标元件配置功能开发







```mermaid
graph TD
    新建可视化页面画板 --> 使用现有组件或自定义组件创建页面布局 --> 控制组件展示方式和交互 --> 控制组件数据展示 --> 页面预览 --满意--> 页面发布 --> 生成页面地址 --> 页面上线
   
    页面预览 --不满意--> 使用现有组件或自定义组件创建页面布局
    
    页面预览 --不满意--> 控制组件展示方式和交互
    
    页面预览 --不满意--> 控制组件数据展示

```







```mermaid
graph TD
    奥格系统确认`1.字段中文2.字段值域3.是否必填4.必填条件` --> 对照数据库结构确定字段英文 --> 产出值域质检sql --> 配置dxp值域质检流程 --> dxp值域质检流程验证`一轮`--> 修改sql和dxp流程 --> dxp值域质检流程验证`二轮` --> 正式运行dxp质检流程 --> 数据入库 --> 数据处理`未实现`
    
    
     对照数据库结构确定字段英文 --> 产出必填质检sql --> 配置dxp必填质检流程 --> dxp必填质检流程验证`一轮` --> 修改sql和dxp流程 --> dxp必填质检流程验证`二轮` --> 正式运行dxp质检流程
    
```

> 【腾讯文档】代码项质检规则
> https://docs.qq.com/sheet/DR2FWdFhWdE9ZUXFx?tab=lqjjml
>
> 【腾讯文档】房屋市政必填字段
> https://docs.qq.com/sheet/DR1Z5R0xPUmxoU09N?tab=f2eajn





```
'../../../datapush/css/images/checked.svg'
"../../../datapush/css/images/check.svg"
```





```
docker run -d --name kingbasev8r6 -p 54321:54321 \
-e SYSTEM_USER=kingbasees \
-e SYSTEM_PWD=kingbasees \
-e ENCODING=UTF8 \
-e DATABASE_MODE=pg  \
-e BLOCK_SIZE=8  \
-v /opt/kingbase/license.dat:/opt/kingbase/Server/bin/license.dat \
chyiyaqing/kingbase:v8r6


cp -r bin/* /opt/kingbase/Server/bin/
cp -r lib/* /opt/kingbase/Server/lib/
cp -r share/extension/* /opt/kingbase/Server/share/extension/
```

```mermaid
journey
title My working day
section Go to work
Make tea: 5: Me
Go upstairs: 3: Me
Do work: 1: Me, Cat
section Go home
Go downstairs: 5: Me
Sit down: 3: Me
```







