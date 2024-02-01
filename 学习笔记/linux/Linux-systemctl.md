## `systemctl`

`systemctl`用于运行`/usr/lib/systemd/system`文件夹下的 `.service`文件启动服务

### 创建`.service`文件

`.service`文件分为三个部分

1. **`Unit`**[`systemd.unit`中文手册](https://www.jinbuguo.com/systemd/systemd.unit.html#)

   | 参数名称      | 参数说明                                                     |
   | ------------- | ------------------------------------------------------------ |
   | Description   | 一段描述这个 Unit 文件的文字，通常只是简短的一句话。         |
   | Documentation | 指定服务的文档，可以是一个或多个文档的URL路径。              |
   | Requires      | 依赖的其他 Unit 列表，列在其中的 Unit 模块会在这个服务启动的同时被启动，并且如果其中有任意一个服务启动失败，这个服务也会被终止 |
   | After         | 与 Requires 相似，但会在后面列出的所有模块全部启动完成以后，才会启动当前的服务。 |
   | Before        | 与 After 相反，在启动指定的任一个模块之前，都会首先确保当前服务已经运行。 |
   | Wants         | 与 Requires 相似，但只是在被配置的这个 Unit 启动时，触发启动列出的每个 Unit 模块，而不去考虑这些模块启动是否成功。 |
   | Conflicts     | 与这个模块有冲突的模块，如果列出模块中有已经在运行的，这个服务就不能启动，反之亦然。 |
   | OnFailure     | 当这个模块启动失败时，就自动启动列出的每个模块。             |

2. **`Service`**[ `systemd.service`中文手册](https://www.jinbuguo.com/systemd/systemd.service.html)

   | 参数名称         | 参数说明                                                     |
   | ---------------- | ------------------------------------------------------------ |
   | Type             | 服务的类型，常用的有 simple（默认类型） 和 forking。默认的 simple 类型可以适应于绝大多数的场景，因此一般可以忽略这个参数的配置。而如果服务程序启动后会通过 fork 系统调用创建子进程，然后关闭应用程序本身进程的情况，则应该将 Type 的值设置为 forking，否则 systemd 将不会跟踪子进程的行为，而认为服务已经退出。 |
   | Environment      | 为服务添加环境变量                                           |
   | EnvironmentFile  | 指定加载一个包含服务所需的环境变量列表的文件，文件中的每一行都是一个环境变量的定义。 |
   | ExecStart        | 这个参数是几乎每个 .service 文件都会有的，指定服务启动的主要命令，在每个配置文件中只能使用一次。（需要使用绝对路径） |
   | ExecStartPre     | 指定在启动执行 ExecStart 的命令前的准备工作，可以有多个，所有命令会按照文件中书写的顺序依次被执行。 |
   | ExecStartPost    | 指定在启动执行 ExecStart 的命令后的收尾工作，也可以有多个。  |
   | ExecStop         | 停止服务所需要执行的主要命令。（需要使用绝对路径）           |
   | ExecStopPost     | 指定在 ExecStop 命令执行后的收尾工作，也可以有多个。         |
   | ExecReload       | 重新加载服务所需执行的主要命令。（需要使用绝对路径）         |
   | Restart          | 这个值用于指定在什么情况下需要重启服务进程。常用的值有 no，on-success，on-failure，on-abnormal，on-abort 和 always。默认值为 no，即不会自动重启服务。这些不同的值分别表示了在哪些情况下，服务会被重新启动 |
   | RestartSec       | 如果服务需要被重启，这个参数的值为服务被重启前的等待秒数。   |
   | Nice             | 服务的进程优先级，值越小优先级越高，默认为0。-20为最高优先级，19为最低优先级。 |
   | WorkingDirectory | 指定服务的工作目录。                                         |
   | RootDirectory    | 指定服务进程的根目录（ / 目录），如果配置了这个参数后，服务将无法访问指定目录以外的任何文件。 |
   | User             | 指定运行服务的用户，会影响服务对本地文件系统的访问权限。     |
   | Group            | 指定运行服务的用户组，会影响服务对本地文件系统的访问权限。   |
   | PrivateTmp       | 是否给服务分配独立的临时空间（true/false）                   |

   

3. **`Install`**[`systemd.unit`中文手册](https://www.jinbuguo.com/systemd/systemd.unit.html#)

   | 参数名称   | 参数说明                                                     |
   | ---------- | ------------------------------------------------------------ |
   | WantedBy   | 和前面的 Wants 作用相似，只是后面列出的不是服务所依赖的模块，而是依赖当前服务的模块。`WantedBy=multi-user.target` 表明当系统以多用户方式（默认的运行级别）启动时，这个服务需要被自动运行。当然还需要 systemctl enable 激活这个服务以后自动运行才会生效。 |
   | RequiredBy | 和前面的 Requires 作用相似，同样后面列出的不是服务所依赖的模块，而是依赖当前服务的模块。 |
   | Also       | 当这个服务被 enable/disable 时，将自动 enable/disable 后面列出的每个模块。 |

   

```shell
[Unit]
Description=Pal Service
After=network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
ExecStart=bash /home/steam/palServerStart.sh
User=steam
Group=steam
Restart=always
RestartSec=5
StartLimitInterval=0
RestartPreventExitStatus=SIGKILL

[Install]
WantedBy=multi-user.target
```

> [!TIP]
>
> 编写完后需要执行执行如下命令
>
> ```shell
> # 重载服务
> systemctl daemon-reload
> ```

[systemd实践: 依据情况自动重启服务 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/56889721)

### 服务管理

| 指令                                      | 作用                             |
| ----------------------------------------- | -------------------------------- |
| `systemctl start` 服务名                  | 开启服务                         |
| `systemctl stop` 服务名                   | 关闭服务                         |
| `systemctl status` 服务名                 | 显示状态                         |
| `systemctl restart` 服务名                | 重启服务                         |
| `systemctl enable` 服务名                 | 开机启动服务                     |
| `systemctl disable` 服务名                | 禁止开机启动                     |
| `systemctl list-units`                    | 查看系统中所有正在运行的服务     |
| `systemctl list-unit-files`               | 查看系统中所有服务的开机启动状态 |
| `systemctl list-dependencies` 服务名      | 查看系统中服务的依赖关系         |
| `systemctl set-default multi-user.target` | 开机时不启动图形界面             |
| `systemctl set-default graphical.target`  | 开机时启动图形界面               |

#### 服务状态

`systemctl status`服务名

![image-20240129170732353](https://xmls-typora-pic.oss-cn-shanghai.aliyuncs.com/pic/image-20240129170732353.png)

**`Active: active (running) since Mon 2024-01-29 16:00:02 CST; 1h 6min ago`**  表示为具体状态，有如下几种情况

| 状态                | 含义                                                   |
| ------------------- | ------------------------------------------------------ |
| **active(running)** | 表示程序正在执行                                       |
| **atcive(exited)**  | 执行一次就正常退出的服务,不在系统中执行任何程序        |
| **active(waiting)** | 正在执行中,处于阻塞状态,需要等待其他程序执行完才能执行 |
| **inactive (dead)** | 未启动状态                                             |

**`Loaded: loaded (/lib/systemd/system/pal.service; disabled; vendor preset: enabled)`**  (配置文件地址 **;** 服务当前的启动状态 **;** 服务默认的启动状态)
服务当前的启动状态有如下几种情况

| 启动状态     | 含义                 |
| ------------ | -------------------- |
| **inactive** | 服务关闭             |
| **disable**  | 服务开机不启动       |
| **enabled**  | 服务开机启动         |
| **static**   | 服务开机启动项被管理 |
| **failed**   | 服务配置错误         |
