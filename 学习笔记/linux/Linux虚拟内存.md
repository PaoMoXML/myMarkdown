### 查看内存大小

```shell
$ free -m
               total        used        free      shared  buff/cache   available
内存：      14765       11645         624          18        2494        2780
交换：       4095        1663        2432
```

### 创建虚拟内存配置文件

```shell
mkdir swap  #新建文件夹
cd swap

# bs 为块的大小，count 创建多少个块
sudo dd if=/dev/zero of=swapfile bs=1M count=2048

# 修改权限
sudo chmod 0600 swapfile

#把生成的文件转换成 Swap 文件
sudo mkswap swapfile

# 激活文件
sudo swapon swapfile
```

> [!TIP]
>
> 再次查看可以发现新增了2G虚拟内存

### 释放虚拟内存

```shell
# 执行命令后，删除创建的swap目录即可
sudo swapoff swapfile
```

### 如果需要开机自动使用该虚拟内存的话，则需要加入到启动脚本

```shell
#此时开的虚拟内存会在开机后消失,如果永久保持下去,在/etc/fstab文件尾添加一下信息:
swapfilepath swap swap defaults 0 0
#例如:我的我是在/home/steam/swap进行的配置,因此添加了下面内容
/home/steam/swap/swapfile swap swap defaults 0 0
#如此,保存并退出即可
```

