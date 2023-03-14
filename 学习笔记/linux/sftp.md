### 1.创建连接

```shell
sftp user_name@remote_server_address[:path]
#sftp root@upaomo.cn

#如果远程服务器自定义了连接的端口，可以使用 -P 参数：
#sftp -P remote_port user_name@remote_server_address[:path]
```

连接成功后会显示`sftp>`

![image-20230313094603018](https://xmls-typora-pic.oss-cn-shanghai.aliyuncs.com/pic/image-20230313094603018.png)

#### 连接参数详解

- `-B`: buffer_size，制定传输 buffer 的大小，更大的 buffer 会消耗更多的内存，默认为 32768 bytes；
- `-P`: port，制定连接的端口号；
- `-R`: num_requests，制定一次连接的请求数，可以略微提升传输速度，但是会增加内存的使用量。

### 常用命令

```shell
#显示当前的工作目录
pwd
#查看当前目录的内容
ls
#使用 -la 参数可以以列表形式查看，并显示隐藏文件
ls -la
#切换目录
cd /opt
#建立文件夹
mkdir

#操作本地目录，在命令前加上l，如：
lls
lcd

#下载文件 get命令
get xxx
#使用 -r 参数可以拉取整个目录
get -r xxx
#把远程的当前目录下的 所有 子目录及里面的 文件，按远程子目录名及文件取下来。
get -r ./.
#把远程目录 当前目录下 所有的 202003开头的目录 里面的文件全部取下来。
get -r 202003*/.

#从本地上传文件到服务器
put xxx
#同样的，可以使用 -r 参数来上传整个目录，但是有一点要注意，如果服务器上不存在这个目录需要首先新建
sftp> mkdir xxx
sftp> put -r xxx

#执行本地 Shell 命令
![command]
```

