[Docker安装Rabbitmq超详细教程_rabbitmq docker-CSDN博客](https://blog.csdn.net/qq_45502336/article/details/118699251)

[(287条消息) docker下安装RabbitMQ延迟队列插件_magic_1024的博客-CSDN博客](https://blog.csdn.net/magic_1024/article/details/103840681)







[如何在 Debian 11 上安装和配置 RabbitMQ (linux-console.net)](https://cn.linux-console.net/?p=3205)

```shell
// 启动
systemctl start rabbitmq-server

// plugins目录
/usr/lib/rabbitmq/lib/rabbitmq_server-3.8.9/plugins
// bin 目录
/usr/lib/rabbitmq/bin

---

$ root@xmlshome:/usr/lib/rabbitmq/bin# ls -l
total 8
lrwxrwxrwx 1 root root   45 Apr 11  2021 rabbitmqctl -> ../lib/rabbitmq_server-3.8.9/sbin/rabbitmqctl
lrwxrwxrwx 1 root root   51 Apr 11  2021 rabbitmq-defaults -> ../lib/rabbitmq_server-3.8.9/sbin/rabbitmq-defaults
lrwxrwxrwx 1 root root   54 Apr 11  2021 rabbitmq-diagnostics -> ../lib/rabbitmq_server-3.8.9/sbin/rabbitmq-diagnostics
lrwxrwxrwx 1 root root   46 Apr 11  2021 rabbitmq-env -> ../lib/rabbitmq_server-3.8.9/sbin/rabbitmq-env
lrwxrwxrwx 1 root root   50 Apr 11  2021 rabbitmq-plugins -> ../lib/rabbitmq_server-3.8.9/sbin/rabbitmq-plugins
lrwxrwxrwx 1 root root   49 Apr 11  2021 rabbitmq-queues -> ../lib/rabbitmq_server-3.8.9/sbin/rabbitmq-queues
-rwxr-xr-x 1 root root 1568 Apr 11  2021 rabbitmq-script-wrapper
lrwxrwxrwx 1 root root   49 Apr 11  2021 rabbitmq-server -> ../lib/rabbitmq_server-3.8.9/sbin/rabbitmq-server
-rwxr-xr-x 1 root root  888 Apr 11  2021 rabbitmq-server-wait
lrwxrwxrwx 1 root root   50 Apr 11  2021 rabbitmq-upgrade -> ../lib/rabbitmq_server-3.8.9/sbin/rabbitmq-upgrade
$ root@xmlshome:/usr/lib/rabbitmq/bin# pwd
/usr/lib/rabbitmq/bin
```

