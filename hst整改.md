| 项目名称                                                     | 项目负责人                    | IP地址          | 版本信息 | 备注                  | 整改负责人 | 备注                                                         |
| ------------------------------------------------------------ | ----------------------------- | --------------- | -------- | --------------------- | ---------- | ------------------------------------------------------------ |
| ~~==太仓市建设工程网上招投标系统==~~                         | 曹泽涛(交付服务1部)           | 2.35.116.86     | 4.37.6.8 | /hst/paas/fsp-core/ss | 徐梦麟     | 8月10日更新，录制服务器密码丢失，导致更新失败<br />完成2024年8月27日 |
| ~~通辽市公共资源交易中心电子招投标系统项目~~                 | 邢春光                        | 10.21.239.57    | 4.38.6.4 | /hst/paas/fsp-core/ss | 徐梦麟     | 本周更新<br />2024年8月26日<br />完成2024年8月26日           |
| ~~三河市公共资源交易中心分散评标设备购置项目~~               | 石国庆                        | 192.168.0.251   | 4.37.6.8 | /hst/paas/fsp-core/ss | 徐梦麟     | 已完成<br />2024年8月12日                                    |
| ~~数字宣城 • 公共资源交易管理服务平台~~                      | 黄路琦(交付服务4部)           | 192.168.100.131 | 4.37.6.8 | /hst/paas/fsp-core/ss | 徐梦麟     | 已完成<br />暂时没空 <br />2024年8月8日                      |
| ~~三明市公共资源交易中心电子交易平台升级及维护项目~~         | 陈学洋->陈子详                | 10.10.9.92      | 4.37.6.8 | /hst/paas/fsp-core/ss | 徐梦麟     | 完成<br />2024年8月9日                                       |
| ~~唐山市丰南区双盲评审项目~~                                 | 李君(石家庄分公司)            | 10.205.84.229   | 4.38.6.3 | /hst/paas/fsp-core/ss | 徐梦麟     | 完成<br />2024年8月10日                                      |
| ~~山东港口电子招投标系统升级建设项目~~                       | 程星宇(交付服务1部) -> 曹佳希 | 172.18.8.6      | 4.38.6.4 | /hst/paas/fsp-core/ss | 徐梦麟     | 完成<br />本周日 2024年8月15日<br />云通信后台	172.18.8.6	eproot	Epoint#2024!SD-Gk<br/>音视频录制	172.18.8.7	eproot	Epoint#2024!SD-Gk |
| ~~山西数字政府建设运营有限公司开评标智能监管系统技术服务和配套产品项目~~ | 杨晨                          | 172.27.230.105  | 4.37.7.8 | /hst/paas/fsp-core/ss | 徐梦麟     | 无需整改                                                     |
| ~~泰安市“慧交易”电子交易平台建设项目~~                       | 罗文超(交付服务1部)           | 172.18.130.231  | 4.38.6.3 | /hst/paas/fsp-core/ss | 徐梦麟     | 已完成<br />本周六 2024年8月23日<br />eproot                 |





问题：

1. **直接使用整合命令处理正在编码中的数据，2个命令执行中间会等待60秒**

   运行命令提示没有相关服务  ->  可能分`管理`和`录制`，这个命令需要在`录制`服务器上运行

   ```shell
   systemctl stop hst-record-mix && sleep 60 && systemctl stop hst-record-control && echo "Stop Record Services Success."
   ```

2. 

![image-20240826154605133](C:\Users\xml00\AppData\Roaming\Typora\typora-user-images\image-20240826154605133.png)