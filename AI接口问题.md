### 1. 获取会议概要

> `http://ip:port/aiserver/getmeetingoutline`

问题：

1. **是否缺少了title字段？**

| 接口字段 | 页面需要字段 | 备注     |
| -------- | ------------ | -------- |
| `time`   | `time`       | 时间     |
| `text`   | `text`       | 概要内容 |
|          | `title`      | 简介     |

![image-20250409155856049](C:\Users\xml00\AppData\Roaming\Typora\typora-user-images\image-20250409155856049.png)

![image-20250409160436393](C:\Users\xml00\AppData\Roaming\Typora\typora-user-images\image-20250409160436393.png)

### 2. 获取会议总结

> `http://ip:port/aiserver/getmeetingsummary`

问题：

1. **角色（`role`）是否为专家（`name`）**
2. **对话（`dialog`）是否为专家意见（`content`）**

| 接口字段          | 页面需要字段 | 备注     |
| ----------------- | ------------ | -------- |
| `overall_summary` | `summarize`  | 会议总结 |
| `role_dialog[]`   |              |          |
| `+role`           |              | 角色     |
| `+dialog`         |              | 对话     |
|                   | `+name`      | 专家     |
|                   | `+content`   | 专家意见 |

![image-20250409161526351](C:\Users\xml00\AppData\Roaming\Typora\typora-user-images\image-20250409161526351.png)

### 3.常见问题 

> 好像没有接口

问题：

1. **常见问题是否由总控自己提供**

| 接口字段 | 页面需要字段 | 备注 |
| -------- | ------------ | ---- |
|          | `question`   | 问题 |

![image-20250409162304165](C:\Users\xml00\AppData\Roaming\Typora\typora-user-images\image-20250409162304165.png)

### 4.项目提问

> `http://ip:port/aiserver/question4pro`

问题：

1. **入参没有要求传入问题，不确定是否是ai助手提问用的接口**