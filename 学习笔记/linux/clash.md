### 安装

```shell
# 先创建相关文件夹
$ cd ~
$ cd mkdir clash
$ cd clash

# 下载文件
$ wget https://git.opclash.com/kehuduan/clash/clash-linux-amd64-v1.18.0.gz

# 解压
$ gzip -d clash-linux-amd64-v1.18.0.gz
# 赋予权限
$ chmod +x clash-linux-amd64-v1.18.0

# 首次启动
INFO[0000] Can't find config, create a initial config file 
INFO[0000] Can't find MMDB, start download 
ERRO[0002] create addr 127.0.0.1:7890 tcp listener error. err:listen tcp 127.0.0.1:7890: bind: address already in use
```

### 配置

```shell
# 进入目录
cd ~/.config/clash/

vim config.yaml
# 不允许局域网中其它电脑使用该代理
allow-lan: false
# 设置 clash 管理界面密码
secret: your password
# 允许从外部访问 clash 管理界面，其中端口 9090 可自行配置
external-controller: '0.0.0.0:9090'

# 可以将windows端的配置文件复制进去

# 进入网页端 clash.razord.top 设置端口为9090，密码为刚设置的
```

### # 实现开机自启动
```shell
[Unit]
Description=Clash Proxy

[Service]
WorkingDirectory=/root
ExecStart=/path/to/clash -f ~/.config/clash/config.yaml >/dev/null 2>&1
Type=simple
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

```shell
systemctl daemon-reload
systemctl start clash
systemctl status clash

systemctl enable clash
```

### apt 使用代理

```shell
sudo apt-get -o Acquire::http::proxy="http://127.0.0.1:7890/" update
```

### docker使用代理

```shell
sudo vim /etc/systemd/system/multi-user.target.wants/docker.service


[Service]
# 添加如下内容，需要配合clash
Environment="HTTP_PROXY=http://127.0.0.1:7890/"
Environment="HTTPS_PROXY=http://127.0.0.1:7890/"

# 重启
systemctl daemon-reload
systemctl restart docker
```

### git使用代理

```shell
//设置代理
//http || https
git config --global http.proxy 127.0.0.1:7890
git config --global https.proxy 127.0.0.1:7890

//sock5代理
git config --global http.proxy socks5 127.0.0.1:7891
git config --global https.proxy socks5 127.0.0.1:7891

//查看代理
git config --global --get http.proxy
git config --global --get https.proxy
s
//取消代理
git config --global --unset http.proxy
git config --global --unset https.proxy

```
