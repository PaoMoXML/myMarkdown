### 安装方式参考：

[openKylin 下载地址和安装方法 | openKylin文档平台](https://docs.openkylin.top/zh/开始贡献/openKylin下载地址和安装方法)

### 安装完后如果无法使用ssh远程连接请执行以下步骤

1. 执行以下命令查看是否有`sshd`相关进程

   ```shell
   netstat -natlp
   ```

2. 如果没有进程则需要执行安装

   ```shell
   sudo apt install openssh-server
   ```

3. 如果有则使用以下命令启动服务

   ```shell
   service sshd start
   ```

### 无法使用root账号登录ssh或sftp

1. 执行如下命令设置root密码

   ```shell
   kylin:/root$ sudo passwd root
   [sudo] ky02 的密码：
   新的 密码：
   重新输入新的 密码：
   passwd：已成功更新密码
   ```

2. 设置sshd服务允许使用root登录

   ```shell
   vim /etc/ssh/sshd_config
    
   # PermitRootLogin Without-password
    
   PermitRootLogin yes
    
   # PasswordAuthentication no
   PasswordAuthentication yes
    
   systemctl restart ssh
   ```

### 安装jdk

[Linux安装JDK并配置环境变量 - 详细步骤 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/341775533)