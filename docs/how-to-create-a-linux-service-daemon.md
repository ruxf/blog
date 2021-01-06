在程序开发中我们必不可少要与服务器打交道，如果做后端开发，更需要了解服务器的进程运行方式，并且能够实现守护进程的实现

在 `Node.js` 的项目中，我们通常会使用 `pm2` 来作为 `Node.js` 的守护进行，比如 `pm2 list` 查看已运行的守护进程列表，使用 `pm2 start index.js --name server` 来添加新的 `Node.js` 进程到 `pm2` 的进程池中

当然我们也可以使用系统级别的守护进程，像使用 `Nginx` `Apache` 等软件，通过使用 `systemctl` 来管理系统进程

比如查看 `Nginx` 的进程状态

```bash
systemctl status nginx
# output
● nginx.service - A high performance web server and a reverse proxy server
     Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
     Active: active (running) since Thu 2020-12-17 02:16:33 UTC; 2 weeks 6 days ago
       Docs: man:nginx(8)
   Main PID: 609 (nginx)
      Tasks: 3 (limit: 4713)
     Memory: 11.5M
     CGroup: /system.slice/nginx.service
             ├─609 nginx: master process /usr/sbin/nginx -g daemon on; master_process on;
             ├─610 nginx: worker process
             └─611 nginx: worker process

Dec 17 02:16:26 hongkong-morgon systemd[1]: Starting A high performance web server and a reverse proxy server...
Dec 17 02:16:33 hongkong-morgon systemd[1]: Started A high performance web server and a reverse proxy server.
```

使用系统级别的守护进程需要添加 `service` 文件

## 什么是 `service` 文件

在了解 `service` 之前，需要先知道什么是 `systemd` ，`service` 文件是 `systemd` 运行时的配置文件

查看 `Nginx` 运行时配置文件

```bash
systemctl cat nginx.service
# output
# Stop dance for nginx
# =======================
#
# ExecStop sends SIGSTOP (graceful stop) to the nginx process.
# If, after 5s (--retry QUIT/5) nginx is still running, systemd takes control
# and sends SIGTERM (fast shutdown) to the main process.
# After another 5s (TimeoutStopSec=5), and if nginx is alive, systemd sends
# SIGKILL to all the remaining processes in the process group (KillMode=mixed).
#
# nginx signals reference doc:
# http://nginx.org/en/docs/control.html
#
[Unit]
Description=A high performance web server and a reverse proxy server
Documentation=man:nginx(8)
After=network.target

[Service]
Type=forking
PIDFile=/run/nginx.pid
ExecStartPre=/usr/sbin/nginx -t -q -g 'daemon on; master_process on;'
ExecStart=/usr/sbin/nginx -g 'daemon on; master_process on;'
ExecReload=/usr/sbin/nginx -g 'daemon on; master_process on;' -s reload
ExecStop=-/sbin/start-stop-daemon --quiet --stop --retry QUIT/5 --pidfile /run/nginx.pid
TimeoutStopSec=5
KillMode=mixed

[Install]
WantedBy=multi-user.target
```



从输出结果可以看出配置文件主要分为三个部分 `[Unit]` [`Service`] [`Install`] ，每一部分都是以等号连接的键值对，中间不能有额外的空格。

## 配置参数

**[Unit]**

`[Unit]` 区块主要是配置 `service` 的描述，以及与其他的 `Service` 的关系

- `Description`：简短描述
- `Documentation`：文档地址
- `Requires`：当前 Unit 依赖的其他 Unit，如果它们没有运行，当前 Unit 会启动失败
- `Wants`：与当前 Unit 配合的其他 Unit，如果它们没有运行，当前 Unit 不会启动失败
- `BindsTo`：与`Requires`类似，它指定的 Unit 如果退出，会导致当前 Unit 停止运行
- `Before`：如果该字段指定的 Unit 也要启动，那么必须在当前 Unit 之后启动
- `After`：如果该字段指定的 Unit 也要启动，那么必须在当前 Unit 之前启动
- `Conflicts`：这里指定的 Unit 不能与当前 Unit 同时运行
- `Condition...`：当前 Unit 运行必须满足的条件，否则不会运行
- `Assert...`：当前 Unit 运行必须满足的条件，否则会报启动失败

`[Service]` 区块在守护进程的类型为 `Service` 时才存在，该区块主要配置 `Service` 的运行时参数以及行为

- `Type`：定义启动时的进程行为。它有以下几种值。

  - `Type=simple`：默认值，执行`ExecStart`指定的命令，启动主进程

  - `Type=forking`：以 fork 方式从父进程创建子进程，创建后父进程会立即退出

  - `Type=oneshot`：一次性进程，Systemd 会等当前服务退出，再继续往下执行

  - `Type=dbus`：当前服务通过D-Bus启动

  - `Type=notify`：当前服务启动完毕，会通知`Systemd`，再继续往下执行

  - `Type=idle`：若有其他任务执行完毕，当前服务才会运行

- `ExecStart`：启动当前服务的命令

- `ExecStartPre`：启动当前服务之前执行的命令

- `ExecStartPost`：启动当前服务之后执行的命令

- `ExecReload`：重启当前服务时执行的命令

- `ExecStop`：停止当前服务时执行的命令

- `ExecStopPost`：停止当其服务之后执行的命令

- `RestartSec`：自动重启当前服务间隔的秒数

- `Restart`：定义何种情况 Systemd 会自动重启当前服务，可能的值包括`always`（总是重启）、`on-success`、`on-failure`、`on-abnormal`、`on-abort`、`on-watchdog`

- `TimeoutSec`：定义 Systemd 停止当前服务之前等待的秒数

- `Environment`：指定环境变量

`[Install]` 通常是配置文件的最后一个区块，定义服务的启动、重启、关闭的行为

- `WantedBy`：它的值是一个或多个 Target，当前 Unit 激活时（enable）符号链接会放入`/etc/systemd/system`目录下面以 Target 名 + `.wants`后缀构成的子目录中
- `RequiredBy`：它的值是一个或多个 Target，当前 Unit 激活时，符号链接会放入`/etc/systemd/system`目录下面以 Target 名 + `.required`后缀构成的子目录中
- `Alias`：当前 Unit 可用于启动的别名
- `Also`：当前 Unit 激活（enable）时，会被同时激活的其他 Unit

Unit 配置文件的完整字段清单，请参考[官方文档](https://www.freedesktop.org/software/systemd/man/systemd.unit.html)



## 实战

以下是在 `Root` 用户目录下 添加一个脚本输出当前时间，并添加执行权限

```bash
#!/usr/bin/bash

# output current time per 5 second
function main() {
		while :
		do
				echo $(date)
				sleep 5
		done
}

main
```

```bash
 chmod +x now
```

执行测试

```bash
now

# output
Wed Jan 6 17:30:16 CST 2021
```



添加 `Service` 文件

```bash
vim nowd.service
```

添加 `Service` 内容

```bash
[Unit]
Description=now

[Service]
ExecStart=/usr/local/bin/now
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s TERM $MAINPID
Restart=always

[Install]
WantedBy=default.target
```

将命令 `now` 和 `Service` 文件软连接到系统的 `systemd` 配置文件目录

```bash
# link now script to /usr/local/bin directory
ln -s /root/now /usr/local/bin/now

# link service file to systemd config directory
ln -s /root/nowd.service /usr/lib/systemd/system/nowd.service
```

将配置文件添加到系统配置目录中之后通过执行 `systemctl cat nowd.service` 查看是否链接成功

```bash
systemctl cat nowd.service

# output 
# /root/nowd.service
[Unit]
Description=now

[Service]
ExecStart=/usr/local/bin/now
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s TERM $MAINPID
Restart=always

[Install]
WantedBy=default.target
```

然后通过 `systemctl` 命令执行守护进程，并查看执行状态

```bash
# start now daemon
systemctl start nowd.service

# view now daemon status
systemctl status nowd.service

# stop now daemon status
systemctl stop nowd.service
```

我们可以通过查看进程的日志来查看输出的日志

```bash
journalctl -f 

# output
1月 06 17:29:01 iz2zeacp86oa3gdr655z7oz now[28375]: Wed Jan 6 17:29:01 CST 2021
1月 06 17:29:06 iz2zeacp86oa3gdr655z7oz now[28375]: Wed Jan 6 17:29:06 CST 2021
1月 06 17:29:11 iz2zeacp86oa3gdr655z7oz now[28375]: Wed Jan 6 17:29:11 CST 2021
1月 06 17:29:16 iz2zeacp86oa3gdr655z7oz now[28375]: Wed Jan 6 17:29:16 CST 2021
1月 06 17:29:21 iz2zeacp86oa3gdr655z7oz now[28375]: Wed Jan 6 17:29:21 CST 2021
1月 06 17:29:26 iz2zeacp86oa3gdr655z7oz now[28375]: Wed Jan 6 17:29:26 CST 2021
1月 06 17:29:31 iz2zeacp86oa3gdr655z7oz now[28375]: Wed Jan 6 17:29:31 CST 2021
1月 06 17:29:36 iz2zeacp86oa3gdr655z7oz now[28375]: Wed Jan 6 17:29:36 CST 2021
1月 06 17:29:41 iz2zeacp86oa3gdr655z7oz now[28375]: Wed Jan 6 17:29:41 CST 2021
1月 06 17:29:46 iz2zeacp86oa3gdr655z7oz now[28375]: Wed Jan 6 17:29:46 CST 2021
1月 06 17:29:51 iz2zeacp86oa3gdr655z7oz now[28375]: Wed Jan 6 17:29:51 CST 2021
1月 06 17:29:56 iz2zeacp86oa3gdr655z7oz now[28375]: Wed Jan 6 17:29:56 CST 2021
```

可以看到系统守护进行在每 5 秒即执行一次 `now` 的命令

