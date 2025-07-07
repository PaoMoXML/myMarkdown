### 1.安装nginx

```shell
#下载nginx源码包
wget http://nginx.org/download/nginx-1.18.0.tar.gz
 
#解压
tar -zxvf nginx-1.18.0.tar.gz
 
#更新
apt-get update
 
#安装 nginx 需要先将官网下载的源码进行编译，编译依赖 gcc 环境。安装指令如下：
sudo apt-get install gcc
sudo apt-get install g++
 
#zlib库提供了开发人员的压缩算法，在Nginx的各种模块中需要使用gzip压缩。安装指令如下:
apt-get install zlib1g
apt-get install zlib1g.dev
 
#Nginx的Rewrite模块和HTTP核心模块会使用到PCRE正则表达式语法。这里需要安装两个安装包pcre和pcre-devel。第一个安装包提供编译版本的库，而第二个提供开发阶段的头文件和编译项目的源代码。安装指令如下：
sudo apt-get install libpcre3 libpcre3-dev 
 
#nginx不仅支持 http协议，还支持 https（即在 ssl 协议上传输 http），如果使用了 https，需要安装 OpenSSL 库。安装指令如下：
#sudo apt-get install openssl libssl-dev
 
cd nginx-1.18.0
 
#额外说明：如果需要开始https支持，这里请不要直接执行./configure，即不要直接执行该脚本，而是在该脚本后面加上SSL模块，请执行如下命令替代 ./confingure 
#如果需要安装到指定的目录文件夹下，需要在此处指定路径，自定义的路径（/home/gaochao/nginx）
#./configure --prefix=/home/xml/nginx --with-http_ssl_module
#./configure --with-http_ssl_module
./confingure 
 
//安装
make && make install
```

### 2.启动Nginx

```shell
#进入/usr/local/nginx/sbin目录，输入./nginx即可启动nginx
./nginx
 
#关闭nginx
./nginx -s quit  或者 ./nginx -s stop
 
#重启nginx
./nginx -s reload
 
#查看nginx进程
ps aux|grep nginx
 
#设置nginx开机启动，只需在rc.local增加启动代码,在底部增加/usr/local/nginx/sbin/nginx
vim /etc/rc.local
```

### 3.升级nginx版本

```shell
#关闭nginx
./sbin/nginx -s quit		#使nginx有序关闭服务
 mv sbin/nginx  sbin/nginx.old		#旧版本备份
 ps -ef |grep nginx |grep -v grep   #查看nginx是否关闭（对于nginx代理线程多的业务来说关闭需要一定的时间）
#解压并编译 
tar -xvf nginx-1.23.2.tar.gz			#解压到当前目录
cd nginx-1.23.2							#进入目录
./configure
make -j4								#这里的-j参数代表启用多核编译（数值不要超过真实cpu数量即可）
ls objs/nginx							#编译完成后objs下会生成刚才编译的nginx运行程序
cp objs/nginx /srv/program/nginx/sbin/	#拷贝到老版本nginx执行目录sbin下即可
#检验
cd /usr/local/nginx/				#进入目录
./sbin/nginx						#启动
ps -ef |grep nginx |grep -v grep	#查看是否启动
./sbin/nginx -V						#查看版本以及模块参数
```

### 4.Nginx配置

####  添加ipv6支持

```shell
server {
 	...
 	listen       [::]:8000;
 	...
}
```

#### 允许访问目录

```shell
 
 #允许访问目录
 server {
 	...
 	# 对需要展示为文件列表的网站磁盘目录，进行网站虚拟路径配置。虚拟路径为'/markdown'
 	location /markdown{ 
            # 需要被设置为文件列表的网站磁盘目录路径。当设置网站虚拟路径时，要使用alias。因为root用于网站主目录，并且虚拟路径映射中，root只能有一个。而alias可以有多个。
            alias /home/xml/markdown/; 
            try_files $uri $uri/ /index.html;
            index index.php index.html index.htm default.php default.htm default.html;
            sendfile on;   # 开启高效文件传输模式
            autoindex on;  # 开启目录文件列表
            autoindex_exact_size on;  # 显示出文件的确切大小，单位是bytes
            autoindex_localtime on;  # 显示的文件时间为文件的服务器时间
            charset utf-8,gbk;  # 避免中文乱码
            limit_rate 1024k;  # 限速，默认不限速
            #开启文件预览
            if ($request_filename ~* ^.*?\.(txt|doc|pdf|rar|gz|zip|docx|exe|xlsx|ppt|pptx)$){
            add_header Content-Disposition: 'p_w_upload;';
         }
		}
     ...
 }
```

#### http代理

```shell
 http {
 	...
 	upstream rabbitmq {
		server 192.168.1.15:15672;
    }
    server {
    	...
    	location /rabbitmq/ {
		proxy_pass  http://rabbitmq/;
        	proxy_set_header Host $host;
        	proxy_set_header X-Real-IP $remote_addr;
        	proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        	root   html;
        	index  index.html index.htm;
		}
		...
    }
 	...
 }
```

### 5.tcp/udp

[Nginx 基于tcp/udp代理 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/69164457)

1. 首先需要升级nginx到支持stream，如果不支持则需要执行更新步骤，并在编译nginx时执行如下命令

   ```shell
   # ./configure --with-http_ssl_module --with-http_stub_status_module --with-stream
   ./configure --with-stream
   ```

2. 配置转发

   > 此段内容是和`http`一个等级，也就是在最外层

   ```shell
   stream {
       log_format proxy '$remote_addr [$time_local] '
                    '$protocol $status $bytes_sent $bytes_received '
                    '$session_time "$upstream_addr" '
                    '"$upstream_bytes_sent" "$upstream_bytes_received" "$upstream_connect_time"';
   
       access_log /usr/local/nginx/logs/tcp-access.log proxy ;
       open_log_file_cache off;
   
   	# 统一放置，方便管理
   	include tcpConf/*.conf;
   }
   ```

3. 配置转发conf文件

   ```shell
   cd /usr/local/nginx/conf
   mkdir tcpConf
   cd tcpConf
   touch tcp2222.conf
   nano tcp2222.conf
   # 配置如下内容(使用2222端口监听ipv4和ipv6，并转发到2221端口)
   upstream tcp2222 {
       server 192.168.1.15:2221;
   }
   
   server {
       listen 2222; #监听ipv4
       listen [::]:2222; #监听ipv6
       proxy_connect_timeout 8s;
       proxy_timeout 24h;
       proxy_pass tcp2222;
   }
   ```

4. 端口监听

   ```shell
   apt-get install tcpdump
   ip a
   tcpdump -n -v -i enp2s0 port 2222
   ```


### 6.Nginx Service

```shell
// /usr/lib/systemd/system
[Unit]
Description=Nginx Service
Documentation="http://nginx.org/en/docs/"
After=network.target remote-fs.target nss-lookup.target
[Service]
Type=forking
PIDFile=/usr/local/nginx/logs/nginx.pid
ExecStartPre=/usr/local/nginx/sbin/nginx -t
ExecStart=/usr/local/nginx/sbin/nginx
ExecReload=/usr/local/nginx/sbin/nginx -s reload
ExecStop=/bin/kill -s QUIT $MAINPID
PrivateTmp=true
LimitNOFILE=200000
[Install]
WantedBy=multi-user.target
```

### 7.通过acme申请ssl

> [说明 · acmesh-official/acme.sh Wiki (github.com)](https://github.com/acmesh-official/acme.sh/wiki/说明)

1. 安装

   ```shell
   curl https://get.acme.sh | sh -s email=my@example.com
   ```

   默认目录

   ```shell
   ~/.acme.sh/
   ```

   可以执行以下如下命令，给acme设置别名方便使用

   ```shell
   alias acme.sh=~/.acme.sh/acme.sh
   ```

2. 使用dns方式手动生成证书：

   1. 

      ```shell
      acme.sh --issue --dns -d mydomain.com \
       --yes-I-know-dns-manual-mode-enough-go-ahead-please
      ```

   2. ```shell
      acme.sh --renew -d mydomain.com \
        --yes-I-know-dns-manual-mode-enough-go-ahead-please
      ```

   3. ```shell
      // 阿里云 dns自动认证 ==推荐使用这个==
      acme.sh --issue --dns dns_ali -d mydomain.com -d *.mydomain.com
      ```

      可能会提示没有设置Key和Secret，需要去`~/.acme.sh/account.conf`中配置，以阿里云为例子，在此处申请`https://ram.console.aliyun.com/users`

      ```shell
      Ali_Key="<key>"
      Ali_Secret="<secret>"
      ```

3. 在Nginx上安装ssl

   ```shell
   acme.sh --install-cert -d example.com \
   --key-file       /path/to/keyfile/in/nginx/key.pem  \
   --fullchain-file /path/to/fullchain/nginx/cert.pem \
   # 如果没有这个命令可以自己添加，参考 6.Nginx Service
   --reloadcmd     "service nginx force-reload"
   ```

   nginx.conf配置

   ```shell
   server {
    	...
    	listen		 8000;
    	listen       [::]:8000;
    	# 开启ssl
    	ssl on;
    	#ssl证书的pem文件路径
       ssl_certificate  /path/to/fullchain/nginx/cert.pem;
       #ssl证书的key文件路径
       ssl_certificate_key /path/to/keyfile/in/nginx/key.pem;
    	...
   }
   ```

   配置完后重启Nginx
