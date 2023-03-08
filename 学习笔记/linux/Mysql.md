### 1.安装wget 、gnupg 、lsb-release

```shell
apt-get update
apt-get install wget gnupg lsb-release
```

> 官方文档：[MySQL :: MySQL Documentation](https://dev.mysql.com/doc/) 

### 2.安装Mysql

1. 下载MySQL APT 存储库 https://dev.mysql.com/downloads/repo/apt/

   ```shell
   wget https://repo.mysql.com//mysql-apt-config_0.8.24-1_all.deb
   ```

2. dpkg 安装存储库，并选择是否要安装配套工具。 一般选择不。 在下图中移动到ok，回车即可

   ```shell
   dpkg -i mysql-apt-config_0.8.24-1_all.deb
   ```

   ![img](https://img-blog.csdnimg.cn/60a4ede73f744eb793f637620a92e38f.png?x-oss-process=image/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbGdnaXJscw==,size_20,color_FFFFFF,t_70,g_se,x_16)

3. 使用apt 命令进行安装. 如果提示出现依赖包的错误，注意分别安装。中途需设定root密码

   ```shell
   apt-get update
   apt-get install mysql-server
   ```

4. 查看 mysql 是否正常运行，检查版本号

   ```shell
   systemctl status mysql
   mysql --version
   ```

   ![image-20230308121114554](https://xmls-typora-pic.oss-cn-shanghai.aliyuncs.com/pic/image-20230308121114554.png)

5. 安装完可以直接使用，但是新版本在安装过程中没有提示设置root用户密码，使用如下方法设置

   ```shell
   mysql_secure_installation
   ```

   > 过程中提示是否开启 VALIDATE PASSWORD PLUGIN，就是设置密码强度检查，自行选择
   >
   > 如果后面要再修改，可以去 **/etc/mysql/my.cnf** 里面增加：
   >
   > ```shell
   > validate_password=OFF
   > ```
   >
   > 选择是的话会让你选择等级：
   >
   > ```shell
   > LOW    Length >= 8
   > MEDIUM Length >= 8, numeric, mixed case, and special characters
   > STRONG Length >= 8, numeric, mixed case, special characters and dictionary file
   > Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG: 
   > ```
   >
   > 然会会让你输入root密码，注意一定要符合上面你选择的要求：
   >
   > ```shell
   > Please set the password for root here.
   > New password: 
   > Re-enter new password: 
   > ```
   >
   > 输入完之后会显示你的密码的安全性，并提示你是否确认使用这个：
   >
   > ```shell
   > Estimated strength of the password: 50 
   > Do you wish to continue with the password provided?(Press y|Y for Yes, any other key for No) : y
   > ```
   >
   > 最后注意会提示你是否重新加载权限表格：
   >
   > ```shell
   > Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y
   > Success.
   > ```
   >
   > 以上设置完成后发现还是无法使用，采用如下方法：
   >
   > ```mysql
   > mysql> select Host,User,plugin,authentication_string from user;
   > +-----------+------------------+-----------------------+-------------------------------------------+
   > | Host      | User             | plugin                | authentication_string                     |
   > +-----------+------------------+-----------------------+-------------------------------------------+
   > | localhost | root             | auth_socket           |                                           |
   > | localhost | mysql.session    | mysql_native_password | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
   > | localhost | mysql.sys        | mysql_native_password | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
   > | localhost | debian-sys-maint | mysql_native_password | *9FD2128DA457CDBF327277BA59C9DE1BAFA88816 |
   > +-----------+------------------+-----------------------+-------------------------------------------+
   > 4 rows in set (0.00 sec)
   > ```
   >
   > 发现root用户的认证方式是：auth_socket
   >
   > 它只检查用户是否使用套接字进行连接，然后比较用户名，并没有密码验证。
   >
   > 所以我们要修改plugin，并设置密码。
   >
   > ```mysql
   > ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'your_password';
   > ```
   >
   > 注意这两个操作必须一条命令执行，先更改插件然后设置密码将不起作用，它将再次回退到auth_socket。
   >
   > 如果你设置密码的时候提示了以下错误：
   >
   > ```
   > ERROR 1819 (HY000): Your password does not satisfy the current policy requirements
   > ```
   >
   > 需要注意你的密码策略等级。
   >
   > ```mysql
   > mysql> SHOW VARIABLES LIKE 'validate_password%';
   > +--------------------------------------+--------+
   > | Variable_name                        | Value  |
   > +--------------------------------------+--------+
   > | validate_password_check_user_name    | OFF    |
   > | validate_password_dictionary_file    |        |
   > | validate_password_length             | 8      |
   > | validate_password_mixed_case_count   | 1      |
   > | validate_password_number_count       | 1      |
   > | validate_password_policy             | MEDIUM |
   > | validate_password_special_char_count | 1      |
   > +--------------------------------------+--------+
   > 7 rows in set (0.31 sec)
   > ```
   >
   > 其中各字段意思如下：
   >
   > - validate_password_length 固定密码的总长度；
   > - validate_password_dictionary_file 指定密码验证的文件路径
   > - validate_password_mixed_case_count 整个密码中至少要包含大/小写字母的总个数
   > - validate_password_number_count 整个密码中至少要包含阿拉伯数字的个数；
   > - validate_password_policy 指定密码的强度验证等级，默认为 MEDIUM；
   >   - 0/LOW：只验证长度；
   >   - 1/MEDIUM：验证长度、数字、大小写、特殊字符；
   >   - 2/STRONG：验证长度、数字、大小写、特殊字符、字典文件；
   > - validate_password_special_char_count 整个密码中至少要包含特殊字符的个数；
   >
   > 我们可以修改等级：
   >
   > ```shell
   > mysql> set global validate_password_policy=LOW;
   > ```
   >
   > 当然其他字段也可以直接修改，总之设置密码时需要与其匹配。
   >
   > 至此root密码设置成功。

### 3.登录和操作

1. 登入数据库命令行环境

   ```shell
   mysql -u root -p    #使用root账户和安装时设定的密码，进入数据库命令行模式。 如果不带-p 会报错
   ```

2. 设定root账户 拥有所有数据库的权限，并且允许任意ip进行登录，也就是开启 remote

   ```mysql
   # 以下命令为 mysql> 命令行环境下使用
   GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' WITH GRANT OPTION ;
   select User, host from mysql.user;    # 查看所有用户的情况
   ```

3. 若使用 上面的过程仍提示错误：`ERROR 1410 (42000): You are not allowed to create a user with GRANT`，则进入 mysql 这个数据库中，对root 的host参数进行修改

   ```mysql
   #进入mysql这个数据库（以下命令仍在mysql命令行环境下进行）
   use mysql;
   # 直接指定root的host 参数
   update user set host='%' where user='root';
   #将所有数据库的权限开放给root
   GRANT ALL PRIVILEGES ON *.* TO root@'%';
   ```

4. 导入你所备份的数据库文档

   ```mysql
   # 先进入mysql命令行模式，新建数据库。 一般保持和原有数据库的名称一致
   mysql -u root -p
   # 新建空数据库
   mysql> create database wordpress;
   mysql> quit;  # 退出数据库命令行模式
    
   #1. 比较直接的导入方法: mysql -u username -p password database < database.back.sql
   mysql -u wordpress -p 1357986420 wordpress < wordpress.back.sql   #这里用户名和数据库名相同
    
   #2. 在数据库命令行下的导入方法
   mysql -u root -p
   mysql> use wordpress;   #指明要操作的数据库，并进入该数据库
   mysql> set names utf8;  #设定数据库的编码（针对空数据库）
   mysql> source /home/backups/wordpress.back.sql;  #导入的命令，数据库备份文档的绝对路径
   mysql> quit;
   ```

5. 其他的一些命令

   ```mysql
   # 用于显示数据库的默认字符编码的命令
   mysql> show variables like '%character%';
   # 用于显示所有数据库名称的命令
   mysql> show databases;
   # 手动编辑配置文档 
   vim /etc/my.cnf
   # 设置默认字符编码
   [mysql]
   default-character-set = utf8
   [mysqld]
   character-set-server = utf8
   [client]
   default-character-set = utf8
   ```

6. 发生错误时，产生的信息文档的位置 /var/log/mysql/error.log 