### 1.下载系统

> 下载地址：[Debian -- 通用操作系统](https://www.debian.org/index.zh-cn.html)

### 2.安装系统

[(241条消息) 最小化安装debian11_Hermokrates的博客-CSDN博客_debian11安装教程](https://blog.csdn.net/sinat_24092079/article/details/126128711)

> 关闭蜂鸣：
>
> 1：create the file `/etc/modprobe.d/pcspkr-blacklist.conf`
> 2：add the following line: `blacklist pcspkr`
> 3：`sudo depmod -a`
> 4：`sudo update-initramfs -u`
> 5：`reboot`

1. 配置静态ip的网络 执行命令`nano /etc/network/interfaces`

   ```
   # This file describes the network interfaces available on your system
   # and how to activate them. For more information, see interfaces(5).
   
   source /etc/network/interfaces.d/*
   
   # The loopback network interface
   auto lo
   iface lo inet loopback
   
   # The primary network interface
   allow-hotplug eno1
   iface eno1 inet static
   # 静态ip /24是子网掩码
   address 192.168.100.210/24
   # 网关
   gateway 192.168.100.1
   # dns-* options are implemented by the resolvconf package, if installed
   # dns服务器
   dns-nameservers 114.114.114.114
   dns-search epoint
   
   ```

   修改完后建议重启 然后使用ping命令查看检查是否网络已通

   

### 3.更换国内软件源

修改：`/etc/apt/sources.list`

1. **阿里云**

   ```
   deb https://mirrors.aliyun.com/debian/ bullseye main non-free contrib
   deb-src https://mirrors.aliyun.com/debian/ bullseye main non-free contrib
   deb https://mirrors.aliyun.com/debian-security/ bullseye-security main
   deb-src https://mirrors.aliyun.com/debian-security/ bullseye-security main
   deb https://mirrors.aliyun.com/debian/ bullseye-updates main non-free contrib
   deb-src https://mirrors.aliyun.com/debian/ bullseye-updates main non-free contrib
   deb https://mirrors.aliyun.com/debian/ bullseye-backports main non-free contrib
   deb-src https://mirrors.aliyun.com/debian/ bullseye-backports main non-free contrib
   ```

2. **腾讯云**

   ```
   deb https://mirrors.tencent.com/debian/ bullseye main non-free contrib
   deb-src https://mirrors.tencent.com/debian/ bullseye main non-free contrib
   deb https://mirrors.tencent.com/debian-security/ bullseye-security main
   deb-src https://mirrors.tencent.com/debian-security/ bullseye-security main
   deb https://mirrors.tencent.com/debian/ bullseye-updates main non-free contrib
   deb-src https://mirrors.tencent.com/debian/ bullseye-updates main non-free contrib
   deb https://mirrors.tencent.com/debian/ bullseye-backports main non-free contrib
   deb-src https://mirrors.tencent.com/debian/ bullseye-backports main non-free contrib
   ```

3. **清华大学镜像站**

   ```
   deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye main contrib non-free
   deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye main contrib non-free
   deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-updates main contrib non-free
   deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-updates main contrib non-free
   deb https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-backports main contrib non-free
   deb-src https://mirrors.tuna.tsinghua.edu.cn/debian/ bullseye-backports main contrib non-free
   deb https://mirrors.tuna.tsinghua.edu.cn/debian-security bullseye-security main contrib non-free
   deb-src https://mirrors.tuna.tsinghua.edu.cn/debian-security bullseye-security main contrib non-free
   ```

   > [Debian 11 (bullseye) 国内软件源 - Guanglin - 博客园 (cnblogs.com)](https://www.cnblogs.com/liuguanglin/p/debian11_repo.html)
   >
   > [apt命令报证书错误的解决方法------- Certificate verification failed: The certificate is NOT trusted._Chaowanq的博客-CSDN博客](https://blog.csdn.net/Chaowanq/article/details/121559709)

### 4.安装openssh-server

1. 下载安装

   ```shell
   apt-get install openssh-server
   apt-get install openssh-client
   ```

2. 查看进程

   ```shell
   ps -e |grep ssh
   
   #输出:
   1943    ?      00:00:00   ssh-agent        #表示客户端已运行；
   16322  ?      00:00:00   sshd              #表示服务端已运行；
   
   systemctl start ssh.service #手动启动
   vim /etc/ssh/sshd_config # 编辑配置
   ```

3. `ssh`配置

   ```
   Port 22  #设置ssh监听的端口号，默认22端口
   ListenAddress ::
   ListenAddress 0.0.0.0  #指定监听的地址，默认监听所有；
   Protocol 2,1   #指定支持的SSH协议的版本号。'1'和'2'表示仅仅支持SSH-1和SSH-2协议。
   #"2,1"表示同时支持SSH-1和SSH-2协议。#
   HostKey /etc/ssh/ssh_host_rsa_key
   HostKey /etc/ssh/ssh_host_dsa_key
   HostKey /etc/ssh/ssh_host_ecdsa_key
   HostKey /etc/ssh/ssh_host_ed25519_key    #HostKey是主机私钥文件的存放位置; 
   #SSH-1默认是 /etc/ssh/ssh_host_key 。SSH-2默认是 /etc/ssh/ssh_host_rsa_key和
   #/etc/ssh/ssh_host_dsa_key 。一台主机可以拥有多个不同的私钥。"rsa1"仅用于SSH-1，
   #"dsa"和"rsa"仅用于SSH-2。
   UsePrivilegeSeparation yes     #是否通过创建非特权子进程处理接入请求的方法来进行权
   #限分 离。默认值是"yes"。 认证成功后，将以该认证用户的身份创另一个子进程。这样做的目的是
   #为了防止通过有缺陷的子进程提升权限，从而使系统更加安全。
   KeyRegenerationInterval 3600   #在SSH-1协议下，短命的服务器密钥将以此指令设置的时
   #间为周期(秒)，不断重新生成；这个机制可以尽量减小密钥丢失或者黑客攻击造成的损失。设为 0 
   #表示永不重新生成为 3600(秒)。
   ServerKeyBits 1024    #指定服务器密钥的位数
   SyslogFacility AUTH   #指定 将日志消息通过哪个日志子系统(facility)发送。有效值是：
   #DAEMON, USER, AUTH(默认), LOCAL0, LOCAL1, LOCAL2, LOCAL3,LOCAL4, LOCAL5, 
   #LOCAL6, LOCAL7
   LogLevel INFO     #指定日志等级(详细程度)。可用值如下:QUIET, FATAL, ERROR, INFO
   #(默认), VERBOSE, DEBUG, DEBUG1, DEBUG2, DEBUG3,DEBUG 与 DEBUG1 等价；DEBUG2
   # 和 DEBUG3 则分别指定了更详细、更罗嗦的日志输出。比 DEBUG 更详细的日志可能会泄漏用户
   # 的敏感信息，因此反对使用。
   LoginGraceTime 120  #限制用户必须在指定的时限(单位秒)内认证成功，0 表示无限制。默认
   #值是 120 秒;如果用户不能成功登录，在用户切断连接之前服务器需要等待120秒。
   PermitRootLogin yes  #是否允许 root 登录。可用值如下："yes"(默认) 表示允许。
   #"no"表示禁止。"without-password"表示禁止使用密码认证登录。"forced-commands-only"
   #表示只有在指定了 command 选项的情况下才允许使用公钥认证登录，同时其它认证方法全部被禁止。
   #这个值常用于做远程备份之类的事情。
   StrictModes yes       #指定是否要求 sshd(8) 在接受连接请求前对用户主目录和相关的配
   #置文件 进行宿主和权限检查。强烈建议使用默认值"yes"来预防可能出现的低级错误。
   RSAAuthentication yes  #是否允许使用纯 RSA 公钥认证。仅用于SSH-1。默认值是"yes"。
   PubkeyAuthentication yes  #是否允许公钥认证。仅可以用于SSH-2。默认值为"yes"。
   IgnoreRhosts yes    #是否取消使用 ~/.ssh/.rhosts 来做为认证。推荐设为yes。
   RhostsRSAAuthentication no  #这个选项是专门给 version 1 用的，使用 rhosts 档案在 　　　　　　　　　　　　　    
   #/etc/hosts.equiv配合 RSA 演算方式来进行认证！推荐no。
   HostbasedAuthentication no    #这个与上面的项目类似，不过是给 version 2 使用的
   IgnoreUserKnownHosts no          #是否在 RhostsRSAAuthentication 或 
   #HostbasedAuthentication 过程中忽略用户的 ~/.ssh/known_hosts 文件。默认值是"no"。
   #为了提高安全性，可以设为"yes"。
   PermitEmptyPasswords no         #是否允许密码为空的用户远程登录。默认为"no"。
   ChallengeResponseAuthentication no   #是否允许质疑-应答(challenge-response)认         
   #证。默认值是"yes"，所有 login.conf中允许的认证方式都被支持。
   PasswordAuthentication yes      # 是否允许使用基于密码的认证。默认为"yes"。
   KerberosAuthentication no    #是否要求用户为 PasswordAuthentication 提供的密码
   #必须通 过 Kerberos KDC 认证，也就是是否使用Kerberos认证。使用Kerberos认证，服务器
   #需要一个可以校验 KDC identity 的 Kerberos servtab 。默认值是"no"。
   KerberosGetAFSToken no       #如果使用了 AFS 并且该用户有一个 Kerberos 5 TGT，
   #那么开   启该指令后,将会在访问用户的家目录前尝试获取一个 AFS  token 。默认为"no"。
   KerberosOrLocalPasswd yes   #如果 Kerberos 密码认证失败，那么该密码还将要通过其它
   #的 认证机制(比如 /etc/passwd)。默认值为"yes"。
   KerberosTicketCleanup yes    #是否在用户退出登录后自动销毁用户的 ticket 。默认
   #"yes"。
   GSSAPIAuthentication no      #是否允许使用基于 GSSAPI 的用户认证。默认值为"no"。
   #仅用 于SSH-2。
   GSSAPICleanupCredentials yes   #是否在用户退出登录后自动销毁用户凭证缓存。默认值      
   #是"yes"。仅用于SSH-2。
   X11Forwarding no    #是否允许进行 X11 转发。默认值是"no"，设为"yes"表示允许。如果
   #允许X11转发并且sshd代理的显示区被配置为在含有通配符的地址(X11UseLocalhost)上监听。
   #那么将可能有额外的信息被泄漏。由于使用X11转发的可能带来的风险，此指令默认值为"no"。需
   #要注意的是，禁止X11转发并不能禁止用户转发X11通信，因为用户可以安装他们自己的转发器。如
   #果启用了 UseLogin ，那么X11转发将被自动禁止。
   X11DisplayOffset 10    #指定X11 转发的第一个可用的显示区(display)数字。默认值                   
   #是 10 。这个可以用于防止 sshd 占用了真实的 X11 服务器显示区，从而发生混淆。
   PrintMotd no                #登入后是否显示出一些信息呢？例如上次登入的时间、地点等
   #等，预设是 yes ，但是，如果为了安全，可以考虑改为 no ！
   PrintLastLog yes           #指定是否显示最后一位用户的登录时间。默认值是"yes"
   TCPKeepAlive yes       #指定系统是否向客户端发送 TCP keepalive 消息。默认值是"yes"
   #。这种消息可以检测到死连接、连接不当关闭、客户端崩溃等异常。可以设为"no"关闭这个特性。
   UseLogin no               #是否在交互式会话的登录过程中使用 login。默认值是"no"。
   #如果开启此指令，那么 X11Forwarding 将会被禁止，因为 login 不知道如何处理 xauth 
   #cookies 。需要注意的是，login是禁止用于远程执行命令的。如果指定了 
   #UsePrivilegeSeparation ，那么它将在认证完成后被禁用。
   MaxStartups 10        #最大允许保持多少个未认证的连接。默认值是 10 。到达限制后，
   #将不再接受新连接，除非先前的连接认证成功或超出 LoginGraceTime 的限制。
   MaxAuthTries 6     #指定每个连接最大允许的认证次数。默认值是 6 。如果失败认证的次数超
   #过这个数值的一半，连接将被强制断开，且会生成额外的失败日志消息。
   UseDNS no          #指定是否应该对远程主机名进行反向解析，以检查此主机名是否与其IP
   #地址真实对应。
   Banner /etc/issue.net   #将这个指令指定的文件中的内容在用户进行认证前显示给远程用户。
   #这个特性仅能用于SSH-2，默认什么内容也不显示。"none"表示禁用这个特性。
   Subsystem sftp /usr/lib/openssh/sftp-server   #配置一个外部子系统(例如，一个文件
   #传输守   护进程)。仅用于SSH-2协议。值是一个子系统的名字和对应的命令行(含选项和参数)。
   UsePAM yes     #是否使用PAM模块认证
   ```

   ​	常见修改

   ​	允许root登录

   ```
   # 修改前
   PermitRootLogin  without-password
   # 修改后
   PermitRootLogin yes
   ```

5. 重启服务

   ```
   #方法一：
    /etc/init.d/ssh restart
   
   # 方法二：
   service ssh restart
   
   #通过 service ssh status 可以查看服务的状态。
   ```

> [debian系统下安装ssh服务 - 殷大侠 - 博客园 (cnblogs.com)](https://www.cnblogs.com/yinheyi/p/6266748.html)

### 5.安装jdk



### 6.安装Tomcat



### 7.安装Java Service Wrapper

> 官网下载Java Service Wrapper [Download Java Service Wrapper - Java Service Wrapper (tanukisoftware.com)](https://wrapper.tanukisoftware.com/doc/english/download.jsp#stable) jsw下载社区办，其他版本需要注册码

1. 将`wrapper-linux-x86-64-3.5.51.tar.gz`上传至`/opt`

2. 使用以下命令解压，得到目录`wrapper-linux-x86-64-3.5.51`

   ```shell
   tar -zxvc wrapper-linux-x86-64-3.5.51.tar.gz
   ```

3. 使用以下命令 给目录创建一个名为wrapper的快捷方式

   ```
   ln -s wrapper-linux-x86-64-3.5.51 wrapper
   ```

4. 复制`/opt/wrapper-linux-x86-64-3.5.51/src/bin/App.sh.in`到`/opt/tomcat/bin`目录下并重命名为`tomcat`(不带后缀，全名为`tomcat`)

5. 复制`/opt/wrapper-linux-x86-64-3.5.51/src/conf/wrapper.conf.in`到`/opt/tomcat/bin`目录下并重命名为`wrapper.conf`

6. 复制`/opt/wrapper-linux-x86-64-3.5.51/lib`目录下所有文件*（三个jar和一个so）*到`/opt/tomcat/lib`目录下

7. 复制`/opt/wrapper-linux-x86-64-3.5.51/bin/wrapper`到`/opt/tomcat/bin`目录下

8. 修改`/opt/tomcat/bin/tomcat`文件

   ```
   #注册成服务时的服务名
   APP_NAME="tomcat"
   APP_LONG_NAME="Tomcat Application Server"
   #两个复制进来文件的相对路径
   WRAPPER_CMD="./wrapper"
   WRAPPER_CONF="./wrapper.conf"
   ```

   执行命令`chmod 775 /opt/tomcat/bin/tomcat` ==赋予执行权限==

9. 修改`/opt/tomcat/bin/wrapper.config`文件

   ```
   #********************************************************************
   # Wrapper Java Properties
   #********************************************************************
   # Java Application
   #  Locate the java binary on the system PATH:
   # jdk路径
   wrapper.java.command=/home/local/java/jdk8u312-b07/bin/java
   #  Specify a specific java binary:
   # 环境变量
   set.JAVA_HOME=/home/local/java/jdk8u312-b07
   set.CATALINA_BASE=/opt/tomcat_8080
   #wrapper.java.command=%JAVA_HOME%/bin/java
   
   # Tell the Wrapper to log the full generated Java command line.
   #wrapper.java.command.loglevel=INFO
   
   # Java Main class.  This class must implement the WrapperListener interface
   #  or guarantee that the WrapperManager class is initialized.  Helper
   #  classes are provided to do this for you.
   #  See the following page for details:
   #  http://wrapper.tanukisoftware.com/doc/english/integrate.html
   
   # 使用WrapperStartStopApp，这样可以通过命令带start/stop来启动/停止程序。
   wrapper.java.mainclass=org.tanukisoftware.wrapper.WrapperStartStopApp
   
   # Log level for notices about missing Java Classpath entries.
   wrapper.java.classpath.missing.loglevel=WARN
   
   # Java Classpath (include wrapper.jar)  Add class path elements as
   #  needed starting from 1
   
   # 设置执行tomcat的classpath文件
   wrapper.java.classpath.1=../lib/wrapper.jar
   wrapper.java.classpath.2=../bin/bootstrap.jar
   wrapper.java.classpath.3=../bin/tomcat-juli.jar
   
   # Java Library Path (location of Wrapper.DLL or libwrapper.so)
   
   #设置tomcat的lib路径
   wrapper.java.library.path.1=../lib
   
   # Java Bits.  On applicable platforms, tells the JVM to run in 32 or 64-bit mode.
   wrapper.java.additional.auto_bits=TRUE
   
   # Java Additional Parameters
   
   # 设置额外参数
   wrapper.java.additional.1=-server
   #wrapper.java.additional.2=-Djava.util.logging.config.file=../conf/logging.properties
   wrapper.java.additional.3=-Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager
   wrapper.java.additional.4=-Djdk.tls.ephemeralDHKeySize=2048
   wrapper.java.additional.5=-Dcatalina.base=../
   wrapper.java.additional.6=-Dcatalina.home=../
   #wrapper.java.additional.7=-Djava.io.tmpdir=../temp
   #wrapper.java.additional.8=-XX:MaxNewSize=256m
   #wrapper.java.additional.9=-XX:MaxPermSize=512m
   wrapper.java.additional.10=-Duser.timezone=Asia/Shanghai
   wrapper.java.additional.11=-XX:+HeapDumpOnOutOfMemoryError 
   wrapper.java.additional.12=-XX:HeapDumpPath=../logs/heapdump.hprof
   wrapper.java.additional.13=-XX:ErrorFile=../logs/hs_err_pid_%p.log
   wrapper.java.additional.14=-XX:+PrintGCDetails
   wrapper.java.additional.15=-XX:+PrintGCDateStamps
   wrapper.java.additional.16=-Xloggc:../logs/gc.log
   wrapper.java.additional.17=-XX:-OmitStackTraceInFastThrow
   
   # Initial Java Heap Size (in MB)
   # 设置tomcat的JVM初始化堆的大小
   wrapper.java.initmemory=1024
   
   # Maximum Java Heap Size (in MB)
   # 设置tomcat的JVM堆的最大值
   wrapper.java.maxmemory=2048
   
   # Application parameters.  Add parameters as needed starting from 1
   # 设置启动、停止和重启参数
   wrapper.app.parameter.1=org.apache.catalina.startup.Bootstrap
   wrapper.app.parameter.2=1
   wrapper.app.parameter.3=start
   
   wrapper.app.parameter.4=org.apache.catalina.startup.Bootstrap
   wrapper.app.parameter.5=TRUE
   wrapper.app.parameter.6=1
   wrapper.app.parameter.7=stop
   
   #********************************************************************
   # Wrapper Logging Properties
   #********************************************************************
   # Enables Debug output from the Wrapper.
   # wrapper.debug=TRUE
   wrapper.adviser=FALSE
   # Format of output for the console.  (See docs for formats)
   wrapper.console.format=M
   
   # Log Level for console output.  (See docs for log levels)
   wrapper.console.loglevel=INFO
   
   # Log file to use for wrapper output logging.
   # 设置log文件路径，格式
   wrapper.logfile=../logs/wrapper.YYYYMMDD.log
   # 设置log输出模式为每天
   wrapper.logfile.rollmode=DATE
   
   # Format of output for the log file.  (See docs for formats)
   wrapper.logfile.format=M
   
   # Log Level for log file output.  (See docs for log levels)
   wrapper.logfile.loglevel=INFO
   
   # Maximum size that the log file will be allowed to grow to before
   #  the log is rolled. Size is specified in bytes.  The default value
   #  of 0, disables log rolling.  May abbreviate with the 'k' (kb) or
   #  'm' (mb) suffix.  For example: 10m = 10 megabytes.
   
   # 文件最大大小
   wrapper.logfile.maxsize=300m
   
   # Maximum number of rolled log files which will be allowed before old
   #  files are deleted.  The default value of 0 implies no limit.
   # 文件最多存储个数
   wrapper.logfile.maxfiles=180
   
   # Log Level for sys/event log output.  (See docs for log levels)
   wrapper.syslog.loglevel=NONE
   
   #********************************************************************
   # Wrapper General Properties
   #********************************************************************
   # Allow for the use of non-contiguous numbered properties
   wrapper.ignore_sequence_gaps=TRUE
   
   # Do not start if the pid file already exists.
   wrapper.pidfile.strict=TRUE
   
   # Title to use when running as a console
   
   # windows下tomcat控制台名称
   wrapper.console.title=@app.long.name@
   
   #********************************************************************
   # Wrapper JVM Checks
   #********************************************************************
   # Detect DeadLocked Threads in the JVM. (Requires Standard Edition)
   wrapper.check.deadlock=TRUE
   wrapper.check.deadlock.interval=10
   wrapper.check.deadlock.action=RESTART
   wrapper.check.deadlock.output=FULL
   
   # Out Of Memory detection.
   #  Ignore -verbose:class output to avoid false positives.
   wrapper.filter.trigger.1000=[Loaded java.lang.OutOfMemoryError
   wrapper.filter.action.1000=NONE
   # (Simple match)
   wrapper.filter.trigger.1001=java.lang.OutOfMemoryError
   # (Only match text in stack traces if -XX:+PrintClassHistogram is being used.)
   #wrapper.filter.trigger.1001=Exception in thread "*" java.lang.OutOfMemoryError
   #wrapper.filter.allow_wildcards.1001=TRUE
   wrapper.filter.action.1001=RESTART
   wrapper.filter.message.1001=The JVM has run out of memory.
   
   #********************************************************************
   # Wrapper Email Notifications. (Requires Professional Edition)
   #********************************************************************
   # Common Event Email settings.
   #wrapper.event.default.email.debug=TRUE
   #wrapper.event.default.email.smtp.host=<SMTP_Host>
   #wrapper.event.default.email.smtp.port=25
   #wrapper.event.default.email.subject=[%WRAPPER_HOSTNAME%:%WRAPPER_NAME%:%WRAPPER_EVENT_NAME%] Event Notification
   #wrapper.event.default.email.sender=<Sender email>
   #wrapper.event.default.email.recipient=<Recipient email>
   
   # Configure the log attached to event emails.
   #wrapper.event.default.email.maillog=ATTACHMENT
   #wrapper.event.default.email.maillog.lines=50
   #wrapper.event.default.email.maillog.format=LPTM
   #wrapper.event.default.email.maillog.loglevel=INFO
   
   # Enable specific event emails.
   #wrapper.event.wrapper_start.email=TRUE
   #wrapper.event.jvm_prelaunch.email=TRUE
   #wrapper.event.jvm_start.email=TRUE
   #wrapper.event.jvm_started.email=TRUE
   #wrapper.event.jvm_deadlock.email=TRUE
   #wrapper.event.jvm_stop.email=TRUE
   #wrapper.event.jvm_stopped.email=TRUE
   #wrapper.event.jvm_restart.email=TRUE
   #wrapper.event.jvm_failed_invocation.email=TRUE
   #wrapper.event.jvm_max_failed_invocations.email=TRUE
   #wrapper.event.jvm_kill.email=TRUE
   #wrapper.event.jvm_killed.email=TRUE
   #wrapper.event.jvm_unexpected_exit.email=TRUE
   #wrapper.event.wrapper_stop.email=TRUE
   
   # Specify custom mail content
   wrapper.event.jvm_restart.email.body=The JVM was restarted.\n\nPlease check on its status.\n
   
   #********************************************************************
   # Wrapper Windows Service Properties
   #********************************************************************
   # WARNING - Do not modify any of these properties when an application
   #  using this configuration file has been installed as a service.
   #  Please uninstall the service before modifying this section.  The
   #  service can then be reinstalled.
   
   # Name of the service
   # 设置服务名称
   wrapper.name=tomcat
   
   # Display name of the service
   # 设置服务显示名称
   wrapper.displayname=tomcatDisplayname
   
   # Description of the service
   # 设置服务描述
   wrapper.description=This is a Tomcat Process
   
   # Service dependencies.  Add dependencies as needed starting from 1
   wrapper.ntservice.dependency.1=
   
   # Mode in which the service is installed.  AUTO_START, DELAY_START or DEMAND_START
   # 设置允许Tomcat服务自动启动
   wrapper.ntservice.starttype=AUTO_START
   
   # Allow the service to interact with the desktop (Windows NT/2000/XP only).
   wrapper.ntservice.interactive=FALSE
   
   # Allow the current user to perform certain actions without being prompted for
   #  administrator credentials. (Requires Professional Edition)
   #wrapper.ntservice.permissions.1.account=CURRENT_USER
   #wrapper.ntservice.permissions.1.allow=START, STOP, PAUSE_RESUME
   
   # 额外参数
   wrapper.timezone=CNST
   wrapper.ping.timeout.action=DUMP,RESTART
   wrapper.request_thread_dump_on_failed_jvm_exit=TRUE
   wrapper.request_thread_dump_on_failed_jvm_exit.delay=20
   
   ```

10. 执行安装服务 `/opt/tomcat/bin/tomcat install`

    > 移除服务命令 /opt/tomcat/bin/tomcat remove

11. 执行启动命令 `service tomcat start`





#### 设置笔记本盒盖不待机

```shell
nano /etc/systemd/logind.conf
```

修改`HandleLidSwitch=lock`



#### 重新加载服务

```shell
systemctl daemon-reload
```



### 8.安装nginx

1. 安装nginx

```shell

//下载nginx源码包
wget http://nginx.org/download/nginx-1.18.0.tar.gz
 
//解压
tar -zxvf nginx-1.18.0.tar.gz
 
//更新
apt-get update
 
//安装 nginx 需要先将官网下载的源码进行编译，编译依赖 gcc 环境。安装指令如下：
sudo apt-get install gcc
sudo apt-get install g++
 
//zlib库提供了开发人员的压缩算法，在Nginx的各种模块中需要使用gzip压缩。安装指令如下:
apt-get install zlib1g
apt-get install zlib1g.dev
 
//Nginx的Rewrite模块和HTTP核心模块会使用到PCRE正则表达式语法。这里需要安装两个安装包pcre和pcre-devel。第一个安装包提供编译版本的库，而第二个提供开发阶段的头文件和编译项目的源代码。安装指令如下：
sudo apt-get install libpcre3 libpcre3-dev 
 
//nginx不仅支持 http协议，还支持 https（即在 ssl 协议上传输 http），如果使用了 https，需要安装 OpenSSL 库。安装指令如下：
sudo apt-get install openssl libssl-dev
 
cd nginx-1.18.0
 
//额外说明：如果需要开始https支持，这里请不要直接执行./configure，即不要直接执行该脚本，而是在该脚本后面加上SSL模块，请执行如下命令替代 ./confingure 
//如果需要安装到指定的目录文件夹下，需要在此处指定路径，自定义的路径（/home/gaochao/nginx）
//  ./configure --prefix=/home/gaochao/nginx --with-http_ssl_module
./configure --with-http_ssl_module
 
//安装
make && make install
```

2. 启动nginx

```shell
//进入/usr/local/nginx/sbin目录，输入./nginx即可启动nginx
./nginx
 
//关闭nginx
./nginx -s quit  或者 ./nginx -s stop
 
//重启nginx
./nginx -s reload
 
//查看nginx进程
ps aux|grep nginx
 
//设置nginx开机启动，只需在rc.local增加启动代码,在底部增加/usr/local/nginx/sbin/nginx
vim /etc/rc.local
```

3. 升级nginx版本

```shell
//关闭nginx
./sbin/nginx -s quit		//使nginx有序关闭服务
 mv sbin/nginx  sbin/nginx.old		//旧版本备份
 ps -ef |grep nginx |grep -v grep   //查看nginx是否关闭（对于nginx代理线程多的业务来说关闭需要一定的时间）
//解压并编译 
tar -xvf nginx-1.23.2.tar.gz			//解压到当前目录
cd nginx-1.23.2							//进入目录
./configure
make -j4								//这里的-j参数代表启用多核编译（数值不要超过真实cpu数量即可）
ls objs/nginx							//编译完成后objs下会生成刚才编译的nginx运行程序
cp objs/nginx /srv/program/nginx/sbin/	//拷贝到老版本nginx执行目录sbin下即可
//检验
cd /usr/local/nginx/				//进入目录
./sbin/nginx						//启动
ps -ef |grep nginx |grep -v grep	//查看是否启动
./sbin/nginx -V						//查看版本以及模块参数
```



### 9.安装无线网卡驱动

1. 使用如下命令查看网卡型号

   ```shell
   lspci -nn
   ```

   > Network controller [0280]: Realtek Semiconductor Co., Ltd. RTL8822CE 802.11ac PCIe Wireless Network Adapter [10ec:c822]

2. 根据网卡型号安装驱动

   ```shell
   apt install firmware-realtek
   ```

3. 重启电脑

   ```shell
   reboot
   
   //查看网卡
   ip addr
   // wlp4s0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen //1000 link/ether 90:0f:0c:86:a5:a3 brd ff:ff:ff:ff:ff:ff
   
   ```

### 10.常用命令

```shell
# 查看进程所用pid
ps -ef | grep 应用名
#查看进程的所用端口
netstat -nap | grep pid
#根据端口port查进程
netstat -nap | grep port
```

