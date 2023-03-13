### 1.安装nginx

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

### 2.启动Nginx

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

### 3.升级nginx版本

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

### 4.Nginx配置

```

```

