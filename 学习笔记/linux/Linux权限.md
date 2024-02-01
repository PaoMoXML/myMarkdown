### 权限概述

#### Linux中权限基于UGO模型进行控制

>  [!IMPORTANT]
>
> U --> user -- 用户
> G --> group -- 组
> O --> other -- 其他

---

#### 查看权限及理解

```shell
# 查看某文件夹
$ ls -ld repository
drwxrwxrwx 4 xumenglin xumenglin 4096 12月  7 16:53 repository/
# 查看某文件
$ ls -ld pom.xml
-rw-rw-r-- 1 xumenglin xumenglin 1054 10月 31 09:49 pom.xml
```

说明：
文件夹`repository`的权限为`drwxrwxrwx`，第一个字符意为文件类型，后面的每三个为一组分别是文件属主(Owner)、用户组(Group)、其他(Other)用户的读、写、执行权限

1. 其中 **d** 为文件类型，有如下几种类型

   | 文件类型 | 说明         |
   | -------- | ------------ |
   | **d**    | 文件夹       |
   | **-**    | 普通文件     |
   | **l**    | 链接         |
   | **b**    | 块设备文件   |
   | **p**    | 管道文件     |
   | **c**    | 字符设备文件 |
   | **s**    | 套接口文件   |

2. **`rwx`**意为

   | 权限  | 说明                                           |
   | ----- | ---------------------------------------------- |
   | **r** | 读read 可以列出目录内容 用数字表示为4          |
   | **w** | 写write 可以在目录中创建删除文件 用数字表示为2 |
   | **x** | 执行execute 可以访问目录内容 用数字表示为1     |
   | **-** | 没有权限 用数字表示为0                         |

---

### 修改权限

#### `chown`

>  [!NOTE]
>
> **用于修改文件所属用户、所属组**


```shell
$ chown 用户 文件名/目录名

# 举例
$ ls -l
-rw-r--r-- 1 root root 0  2月  1 16:48 test
# 尝试vim进行编辑，无法保存

$ sudo chown xumenglin test.txt
$ ls -l
-rw-r--r-- 1 xumenglin root 0  2月  1 16:48 test
# 尝试vim进行编辑，仍旧无法保存

# 查看文件夹权限
$ ls -ld
drwxr-xr-x 2 root root 4096  2月  1 16:53 .
$ sudo chown xumenglin ./
$ ls -ld
drwxr-xr-x 2 xumenglin root 4096  2月  1 16:53 .
# 此时使用vim编辑成功
```

#### `chmod`

>  [!NOTE]
>
> **修改文件、目录的权限**
>
> 模式为如下格式：
> 1. u、g、o分别代表用户、组和其他
> 2. a可以代指ugo
> 3. +、-代表加入或删除对应权限
> 4. r、w、x代表三种权限

```bash
$ chmod +模式 +文件

# 举例
$ chmod u+rw test #给所属用户权限位添加读写权限
$ chmod g+rw test #给所属组权限位添加读写权限
$ chmod o+rw test #给其他用户权限位添加读写权限
$ chmod u=rw test #设置所属用户权限位的权限位读写
$ chmod a-x test #所有权限为去掉执行权限

$ chmod u+rwx test
$ ls -l
-rwxr--r-- 1 xumenglin xumenglin 0  2月  1 16:53 test
```

**`chmod`**也支持只用数字方式进行权限修改

| 权限  | 数字  |
| ----- | ----- |
| **r** | **4** |
| **w** | **2** |
| **x** | **1** |

使用数字表示权限时，每组权限分别对应数字之和：

`rwx`＝4+2+1＝7
`rw-`=4+2＝6
`r-x`=4+1＝5
`---`= 0

```bash
$ chmod 755 test
```

### 默认权限`umask`

查看默认权限

```bash
$ umask [-p] -S [mode]

$ umask
0002 # (第一个 0 表示是 8 进制，后面的三位数字用 8 进制表示)
$ umask -S
u=rwx,g=rwx,o=rx # u=user g=group o=other
```

创建一个文件的默认权限（`mode`）为 `rw-rw-rw`，用数字表示就是0666，创建文件夹的默认权限为`rwx-rwx-rwx`，也就是0777。这个默认值需要减去`umask`的值（0002），所以得出文件权限为664（`rw-rw-r--`），文件夹权限为775（`rwx-rwx-r-x`）

想要暂时修改`umask`的值，可以输入

```shell
# 此方法只能在当前terminal中生效
$ umask 022
```

想要永久修改值，需要修改文件**`/etc/bashrc`**，在文件中添加一行 **`umask 022`**

[掌握Linux文件权限，看这篇就够了 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/165091887)

[Linux—umask（创建文件时的掩码）用法详解-CSDN博客](https://blog.csdn.net/Change_Improve/article/details/106107317)



### 常用命令

```bash
# 查看用户信息
$ cat /etc/passwd | grep xumenglin
xumenglin:x:1000:1000:XuMenglin,,,:/home/xumenglin:/bin/bash
# 用户名 : 密码 : 用户id : 用户组id : ??未知 : home目录 : /bin/bash是shell命令所在目录

# 查看所有组信息
$ cat /etc/group
adm:x:4:syslog,xumenglin
xumenglin:x:1000:
# 用户组 : 用户组指令 : 用户组id : 用户组里包含的用户

# 查看当前登录用户的组内成员
$ groups
xumenglin adm cdrom sudo dip plugdev lpadmin lxd sambashare
#查看steam用户所在的组,以及组内成员
$ groups steam
steam : steam sudo
#查看当前登录用户名
$ whoami
```

