## `Corntab`

[Linux crontab 命令 | 菜鸟教程 (runoob.com)](https://www.runoob.com/linux/linux-comm-crontab.html)

### 基础命令

Linux **crontab** 是用来定期执行程序的命令。

 Linux 任务调度的工作主要分为以下两类：

1. 系统执行的工作：系统周期性所要执行的工作，如备份系统数据、清理缓存
2. 个人执行的工作：某个用户定期要做的工作，例如每隔 10 分钟检查邮件服务器是否有新信，这些工作可由每个用户自行设置

语法：

```shell
crontab [ -u user ] file
# 或
crontab [ -u user ] { -l | -r | -e }
```

```shell
# 用于编辑定时任务; 定时任务编辑后，不用重启定时任务，定时任务会自动重新加载。
crontab -e
# 用来查看当前有什么定时任务
crontab -l
# 修改编辑命令用的编辑器
select-editor
```

时间格式如下：

```shell
f1 f2 f3 f4 f5 program
# 每天两点执行
0 2 * * * bash ~/steam/palbackup.sh >> ~/palBackup/backup.log
# 每四个小时执行
0 */4 * * * sudo systemctl restart pal

*    *    *    *    *
-    -    -    -    -
|    |    |    |    |
|    |    |    |    +----- 星期中星期几 (0 - 6) (星期天 为0)
|    |    |    +---------- 月份 (1 - 12) 
|    |    +--------------- 一个月中的第几天 (1 - 31)
|    +-------------------- 小时 (0 - 23)
+------------------------- 分钟 (0 - 59)
```

### 日志相关

#### `cron`执行开启日志

1. 将`/etc/rsyslog.d/50-default.conf` 文件中的`#cron.*`前的#删掉
2. 重启`rsyslog`服务`systemctl restart rsyslog`
3. 重启`cron`服务`systemctl restart cron`

#### 命令执行日志

- 不输出内容

  ```shell
  */5 * * * * /root/XXXX.sh &>/dev/null 2>&1 
  ```

- 将正确和错误的都输出到 xxx.log

  ```shell
  */1 * * * * /root/XXXX.sh > /tmp/load.log 2>&1 &
  ```

- 只输出正确的日志到xxx.log

  ```shell
  */1 * * * * /root/XXXX.sh > /tmp/load.log &  
  # 等同于
  */1 * * * * /root/XXXX.sh 1>/tmp/load.log &
  ```

- 只输出错误的日志到xxx.log

  ```shell
  */1 * * * * /root/XXXX.sh 2> /tmp/load.log & 
  ```

  > [!TIP]
  >
  > 在shell中，每个进程都和三个系统文件相关联：标准输入stdin，标准输出stdout和标准错误stderr，三个系统文件的文件描述符分别为0，1和2。所以这里2>&1的意思就是将标准错误也输出到标准输出当中。
  >
  > \>就相当于 1> 也就是重定向标准输出，不包括标准错误。通过2>&1，就将标准错误重定向到标准输出了（stderr已作为stdout的副本），那么再使用>重定向就会将标准输出和标准错误信息一同重定向了。如果只想重定向标准错误到文件中，则可以使用2> file。
  >
  > ==\> 将覆盖原文件，使用 >> 则是追加到文件==

### 问题

如果日志出现`Skipping @reboot jobs -- not system startup`

1. 把文件`/var/run/crond.reboot`删除
2. 重启`cron`服务`systemctl restart cron`

## `shell`

[Shell 教程 | 菜鸟教程 (runoob.com)](https://www.runoob.com/linux/linux-shell.html)

