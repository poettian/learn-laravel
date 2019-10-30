### 简介

以 c/s 模式运行。

##### 组件构成

**supervisord**

服务端程序。

作用有：

- 开启子 **program**

- 接收客户端命令
- 重启崩溃或异常退出的子 **process**
- 记录子 **process** 的 stdout 和 stderr

配置文件通常位于 `/etc/supervisord.conf`

**supervisorctl**

命令行客户端程序。

通过 UNIX domain socket or an internet (TCP) socket 与服务端通信。

通常配置文件和服务端使用同一个，但是任何包含 `[supervisorctl]` 项的文件也可以。

**Web Server**

**XML-RPC Interface**

### 运行supervisor

##### 运行 **supervisord**

`$BINDIR/supervisord -c 配置文件位置`，也可使用 `systemctl` 启动。

使用 `supervisord --help` 查看可用命令。

##### 运行 supervisorctl

命令格式：`/usr/bin/supervisorctl [options] [action [arguments]]`

使用 `supervisorctl --help` 查看可用 options。

使用 `supervisorctl help` 查看可用 action。

**具体命令解析**：

`sudo supervisorctl start`

`sudo supervisorctl status`

`sudo supervisorctl stop`

`sudo supervisorctl restart`

`sudo supervisorctl reread`

`sudo supervisorctl reload`

`sudo supervisorctl update`

`sudo supervisorctl add`

`sudo supervisorctl remove`

##### 信号

可以发送信号给 **supervisord** pid。

可用信号如下：

```
SIGTERM
```

> **supervisord** and all its subprocesses will shut down. This may take several seconds.

```
SIGINT
```

> **supervisord** and all its subprocesses will shut down. This may take several seconds.

```
SIGQUIT
```

> **supervisord** and all its subprocesses will shut down. This may take several seconds.

```
SIGHUP
```

> **supervisord** will stop all processes, reload the configuration from the first config file it finds, and start all processes.

```
SIGUSR2
```

> **supervisord** will close and reopen the main activity log and all child log files.

### 配置

##### 环境变量

可以使用 `%(ENV_X)s` 格式来引用环境变量。

```ini
[program:example]
command=/usr/bin/example --loglevel=%(ENV_LOGLEVEL)s
```

##### [unix_http_server]

监听 unix domain socket 的 http server 配置。

**supervisorctl** uses XML-RPC to communicate with **supervisord**。

##### [inet_http_server]

监听 tcp socket 的 http server 配置。

##### [supervisord]

服务端配置

##### [supervisorctl]

客户端配置

##### [program:x]

重点中的重点。在这里配置需要 supervisor 管理的任务进程。

**program** 表示一个任务。**x** 是任务的名称，不能包含冒号和括号。`%(program_name)s` 可以被随后的其他配置项使用来引用任务的名称。

实际上 **program** 表示的是一个进程组，其成员由 `numprocs` 和 `process_name` 组合命名而来。如果未定义 `process_name` 项，那么默认开启一个子进程，名称就是 x。

**举个例子**：

配置项 1

```ini
[program:cat]
command=/bin/cat
```

则开启进程情况：

![image-20191018180152783](/Users/tianzhiwei/Code/redis/learn-redis/images/image-20191018180152783.png)

![image-20191018180245893](/Users/tianzhiwei/Code/redis/learn-redis/images/image-20191018180245893.png)

配置项 2

```ini
[program:cat]
process_name=%(program_name)s_%(process_num)02d
command=/bin/cat
numprocs=2
```

![image-20191018180439294](/Users/tianzhiwei/Code/redis/learn-redis/images/image-20191018180439294.png)

![image-20191018180508123](/Users/tianzhiwei/Code/redis/learn-redis/images/image-20191018180508123.png)

这里 **program** 就是 **cat**，**cat_00** **cat_01** 就是 **process**。

这里 **\<gname\>** 就是 **cat**，**cat_00** **cat_01** 就是 **\<name\>**。

**配置项**：

下面的配置项可用的 Python 字符串表达式有：

 `group_name`, `host_node_name`, `process_num`, `program_name`, `here` (the directory of the supervisord config file)

有些配置项略过，详细可参考文档。

**command**

要运行的命令，注意不能以 **daemons** 方式运行。

**process_name**

子进程名称。

**numprocs**

子进程数量，如果大于1，则 `process_name` 必须包含 `%(process_num)s`。

**numprocs_start**

进程偏移量，如果 **numprocs_start=2**，开启的子进程名称为 **cat_02** **cat_03**。进程数量仍然是2。

**priority**

任务开启和关闭的优先级。

**autostart**

设置为true，任务将会在 **supervisord** 启动时自动开启。

**startsecs**

任务开启后必须持续运行的时间，超过此时间才会被视为启动成功，否则视为失败（即使任务返回了一个预期的返回码）。设置为0，则为不计时。

**startretries**

任务失败后的重试次数，默认为3。

**autorestart**

当子进程从 `RUNNING` 状态退出时，是否自动重启。

`false` 不会自动重启

`true ` 自动重启

`unexpected` 如果进程返回码不是 `exitcodes` 配置项的值，则不重启。

**exitcodes**

预期返回码列表，逗号分隔。

**user**

进程运行时的用户身份。

**redirect_stderr**

是否把标准错误信息重定向到标准输出中，类似 `2 > &1`

**stdout_logfile**

进程标准输出的日志文件。

**stderr_logfile**

进程标准错误输入日志文件，如果打开了 `redirect_stderr` ，则此配置无效。

##### [include]

只有 `supervisord` 使用。

##### [group:x]

合并 program 组到 group 组中。

```ini
[program:bar]
***
[program:baz]
***
[group:foo]
programs=bar,baz
priority=999
```

在上面的配置下，组 **bar** 和 **baz** 将不存在，取而代之的是组 **foo** ，原来两个组下的子进程都将被移到 **foo**。

##### [fcgi-program:x]

##### [eventlistener:x]

##### [rpcinterface:x]

