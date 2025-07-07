## 自动安装

```shell
curl -sSL https://get.daocloud.io/docker | sh
```

## 手动安装

### 1.卸载旧版本

Docker 的旧版本被称为 docker，docker.io 或 docker-engine，如果已安装，请卸载它们：

```shell
$ sudo apt-get remove docker docker-engine docker.io containerd runc
```

### 2.安装前配置

#### 设置仓库

1. 更新 apt 包索引。

```shell
$ sudo apt-get update
```

2. 安装 apt 依赖包，用于通过 HTTPS 来获取仓库。

```shell
$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg2 \
    software-properties-common
```

3. 添加 Docker 的官方 GPG 密钥：

```shell
$ curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/debian/gpg | apt-key add -
```

4. 9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88 通过搜索指纹的后8个字符，验证您现在是否拥有带有指纹的密钥。

```shell
$ sudo apt-key fingerprint 0EBFCD88

pub   4096R/0EBFCD88 2017-02-22
      Key fingerprint = 9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
uid                  Docker Release (CE deb) <docker@docker.com>
sub   4096R/F273FCD8 2017-02-22
```

5. 使用以下指令设置稳定版仓库：

```shell
$ sudo add-apt-repository \
   "deb [arch=amd64] https://mirrors.ustc.edu.cn/docker-ce/linux/debian \
  $(lsb_release -cs) \
  stable"
```

### 3.安装 Docker Engine-Community

1. 更新 apt 包索引：

```shell
$ sudo apt-get update
```

2. 安装最新版本的 Docker Engine-Community 和 containerd

```shell
$ sudo apt-get install docker-ce docker-ce-cli containerd.io
```

3. 测试 Docker 是否安装成功，输入以下指令，打印出以下信息则安装成功：

```shell
$ sudo docker run hello-world

Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
1b930d010525: Pull complete                                                                                                                                  Digest: sha256:c3b4ada4687bbaa170745b3e4dd8ac3f194ca95b2d0518b417fb47e5879d9b5f
Status: Downloaded newer image for hello-world:latest


Hello from Docker!
This message shows that your installation appears to be working correctly.


To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.


To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash


Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/


For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

### 4.卸载 docker

删除安装包：

```shell
sudo apt-get purge docker-ce
```

删除镜像、容器、配置文件等内容：

```shell
sudo rm -rf /var/lib/docker
```

### 5.常用命令

```shell
#查看所有容器
docker ps -a
#进入容器内部
docker exec -it '容器名' /bin/bash
#停止容器
docker stop '容器名' 
#删除容器
docker rm '容器名'
#查看日志
docker logs '容器名'
#跟踪最新100条日志
docker logs -f --tail 100 '容器名'
```

root@debian:~# docker ps -a
CONTAINER ID   IMAGE                     COMMAND        CREATED       STATUS          PORTS     NAMES
4394237a195b   sulinggg/openwrt:x86_64   "/sbin/init"   2 hours ago   Up 20 minutes             openwrt

### 安装redis

1. 在容器启动后，再为其配置密码

   ```shell
   # 拉取redis镜像
   docker pull redis
   
   # 启动容器的时候，并为其设置密码
   docker run -d --name myredis -p 6379:6379 redis --requirepass "123456"
   ```

2. 在容器启动后，再为其配置密码

   ```shell
   # 拉取redis镜像
   docker pull redis
   
   # 启动容器
   docker run -d -p 6366:6379 --name redis-test redis
   
   # 查看运行的redis，并记下它的 CONTAINER ID
   docker ps 
   
   # 通过容器id，进入redis
   docker exec -it CONTAINER_ID /bin/bash
   
   # 运行redis客户端
   redis-cli
   
   # 查看redis的密码
   config get requirepass
   
   # 设置redis的密码
   config set requirepass yourPassword
   
   # 认证
   auth yourPassword
   ```

### 使用tomcat运行war





### docker使用代理拉取镜像

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

### 查看镜像占用内存等

```shell
sudo docker stats --no-stream
```

