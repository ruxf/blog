今天要介绍的是如何在浏览器中打开 `VS Code` 并且能够实现和桌面软件一样的开发体验，因此在 `iPad` 和 手机浏览器中随时随地都可以进行代码查看、编辑、运行。

如果说前端开发中哪一款编辑器用起来很顺手，并且有丰富的插件系统，那肯定离不了 `VS Code`，在  `2019` 年的 `StackOverflow`  开发者调研中，`VS Code` 被认为是最受开发者欢迎的开发环境。

## 什么是 code-server

`code-server `  ([地址](https://github.com/cdr/code-server))是国外 `Coder` 团队开源的基于 `VS Code` 运行在服务器上的版本，它可以实现在浏览器中通过 `HTTP` 协议或者 `SSH` 协议来连接到远程编辑器。

有的小伙伴可以会说，现在 `VS Code` 可以通过安装微软推出的  `Remote`套件实现远程访问，但是这种的远程开发还是基于本地安装的 `VS Code` 来实现访问的，而本文中我们要介绍的是只要有一个浏览器，不管是使用的 `iPad` 还是手机，都可以便捷的开发。

以下就是我搭建在远程服务器上运行的 `VS Code` ，通过 `Chrome` 来连接，使用体验和安装在系统中的软件基本一致

![](https://cdn.pongj.com/uPic/20210107-1610020902380-amM4c4.png)



## 如何安装

我的安装环境是在 `Ubuntu 20.04.1 LTS` 的服务器中。

1. 通过脚本安装

   通过执行 `--dry-run` ，可以预先查看安装的步骤，是否缺少相应的软件包

   ```bash
   curl -fsSL https://code-server.dev/install.sh | sh -s -- --dry-run
   ```

   然后执行安装命令

   ```bash
   curl -fsSL https://code-server.dev/install.sh | sh
   ```

   命令的输出结果

   ```bash
   Ubuntu 20.04.1 LTS
   Installing v3.8.0 deb package from GitHub releases.
   
   + mkdir -p ~/.cache/code-server
   + curl -#fL -o ~/.cache/code-server/code-server_3.8.0_amd64.deb.incomplete -C - https://github.com/cdr/code-server/releases/download/v3.8.0/code-server_3.8.0_amd64.deb
   + mv ~/.cache/code-server/code-server_3.8.0_amd64.deb.incomplete ~/.cache/code-server/code-server_3.8.0_amd64.deb
   + dpkg -i ~/.cache/code-server/code-server_3.8.0_amd64.deb
   
   To have systemd start code-server now and restart on boot:
     sudo systemctl enable --now code-server@$USER
   Or, if you don't want/need a background service you can run:
     code-server
   ```

   

2. 手动安装

   **Debian**, **Ubuntu**

   ```bash
   curl -fOL https://github.com/cdr/code-server/releases/download/v3.8.0/code-server_3.8.0_amd64.deb
   sudo dpkg -i code-server_3.8.0_amd64.deb
   sudo systemctl enable --now code-server@$USER
   # Now visit http://127.0.0.1:8080. Your password is in ~/.config/code-server/config.yaml
   ```

   **Fedora**, **CentOS**, **RHEL**, **SUSE**

   ```bash
   curl -fOL https://github.com/cdr/code-server/releases/download/v3.8.0/code-server-3.8.0-amd64.rpm
   sudo rpm -i code-server-3.8.0-amd64.rpm
   sudo systemctl enable --now code-server@$USER
   # Now visit http://127.0.0.1:8080. Your password is in ~/.config/code-server/config.yaml
   ```

   **Arch Linux**

   ```bash
   # Installs code-server from the AUR using yay.
   yay -S code-server
   sudo systemctl enable --now code-server@$USER
   # Now visit http://127.0.0.1:8080. Your password is in ~/.config/code-server/config.yaml
   ```

   ```bash
   # Installs code-server from the AUR with plain makepkg.
   git clone https://aur.archlinux.org/code-server.git
   cd code-server
   makepkg -si
   sudo systemctl enable --now code-server@$USER
   # Now visit http://127.0.0.1:8080. Your password is in ~/.config/code-server/config.yaml
   ```

   **yarn**, **npm**

   ```bash
   yarn global add code-server
   # Or: npm install -g code-server
   code-server
   # Now visit http://127.0.0.1:8080. Your password is in ~/.config/code-server/config.yaml
   ```

   **macOS**

   ```bash
   brew install code-server
   brew services start code-server
   # Now visit http://127.0.0.1:8080. Your password is in ~/.config/code-server/config.yaml
   ```

   **Docker**

   ```bash
   # This will start a code-server container and expose it at http://127.0.0.1:8080.
   # It will also mount your current directory into the container as `/home/coder/project`
   # and forward your UID/GID so that all file system operations occur as your user outside
   # the container.
   #
   # Your $HOME/.config is mounted at $HOME/.config within the container to ensure you can
   # easily access/modify your code-server config in $HOME/.config/code-server/config.json
   # outside the container.
   mkdir -p ~/.config
   docker run -it --name code-server -p 127.0.0.1:8080:8080 \
     -v "$HOME/.config:/home/coder/.config" \
     -v "$PWD:/home/coder/project" \
     -u "$(id -u):$(id -g)" \
     -e "DOCKER_USER=$USER" \
     codercom/code-server:latest
   ```

   

   使用源码安装

   ```bash
   mkdir -p ~/.local/lib ~/.local/bin
   curl -fL https://github.com/cdr/code-server/releases/download/v3.8.0/code-server-3.8.0-linux-amd64.tar.gz \
     | tar -C ~/.local/lib -xz
   mv ~/.local/lib/code-server-3.8.0-linux-amd64 ~/.local/lib/code-server-3.8.0
   ln -s ~/.local/lib/code-server-3.8.0/bin/code-server ~/.local/bin/code-server
   PATH="~/.local/bin:$PATH"
   code-server
   # Now visit http://127.0.0.1:8080. Your password is in ~/.config/code-server/config.yaml
   ```

   

不管是使用脚本安装，还是手动安装，只要能够安装成功，哪种方式都是可以的，毕竟条条大道通罗马嘛

## 配置

在安装完成后脚本默认会在 `/usr/bin` 目录中，添加 `code-server` 可执行文件的软链接

```bash
$ which code-server

# output
/usr/bin/code-server
```

在 `/root` 目录下的  `.cache` 中是 `Github` 的[仓库目录](https://github.com/cdr/code-server)，默认配置文件放置在 `/root/.config/code-server/config.yaml` 文件中

以下是 `config.yaml` 的配置文件

```yaml
# server ip and port
bind-addr: 127.0.0.1:8080
# authority
auth: password
# password
password: password
# whether use https
cert: false
```

可以直接执行 `code-server` ，会默认使用 `/root/.config/code-server/config.yaml` 的配置，然后就可以通过执行的端口和服务器 `ip` 访问到远程的 `VS Code`

```bash
$ code-server

# output
[2021-01-07T13:04:00.413Z] info  Using config file ~/.config/code-server/config.yaml
[2021-01-07T13:04:00.662Z] info  Using user-data-dir ~/.local/share/code-server
[2021-01-07T13:04:00.667Z] info  code-server 3.5.0 de41646fc402b968ca6d555fdf2da7de9554d28a
[2021-01-07T13:04:00.671Z] info  HTTP server listening on http://127.0.0.1:8080
[2021-01-07T13:04:00.672Z] info      - Using password from ~/.config/code-server/config.yaml
[2021-01-07T13:04:00.672Z] info      - To disable use `--auth none`
[2021-01-07T13:04:00.672Z] info    - Not serving HTTPS
```

看到上面的输出结果，我们的安装就成功了

## 守护进程

如果我们想要将 `code-server` 放在系统级的守护进程中执行，就需要我们添加相应的 `service` 文件，然后通过 `systemd` 来管理 `code-server` 进程

添加 `code-server.service` 文件

```bash
$ cat > /usr/lib/systemd/system/code-server.service << EOF
[Unit]
Description=code-server
After=network.target

[Service]
Type=exec
Environment=HOME=/root
ExecStart=/usr/bin/code-server
Restart=always
EOF
```

然后我们可以执行 `systemctl cat code-server.service` 查看添加的 `service` 配置文件

```bash
$ systemctl cat code-server.service

# output 
# /lib/systemd/system/code-server.service
[Unit]
Description=code-server
After=network.target

[Service]
Type=exec
Environment=HOME=/root
ExecStart=/usr/bin/code-server
Restart=always

[Install]
WantedBy=default.target
```

然后我们就可以通过 `systemctl` 来启动、重启和关闭 `code-server` 的守护进程

```bash
# start code-server
$ systemctl start code-server

# reload code-server
$ systemctl reload code-server

# stop code-server
$ systemctl stop code-server

# code-server status
$ systemctl status code-server
```

查看 `code-server` 的运行日志可以通过 `journalctl` 来查看

```bash
journalctl -f

# or
journalctl /lib/systemd/system/code-server.service
```

如果对 `service` 配置文件有疑问的可以查看上一篇关于 `service` 的教程

## 访问

服务成功部署之后，在云服务器的控制台中需要添加相应端口的安全组策略，然后通过 `ip:port` 即可访问到 `code-server` 的欢迎页面

输入配置文件中的密码即可进入到 `VS Code` 的页面了，熟悉的 `VS Code` 页面扑面而来，熟悉的命令和插件都和原生的软件一样哦。

如果有自己的域名的小伙伴可以配合使用 `Nginx` 反向代理 `code-server` ，然后通过自定义的域名来访问，笔者就是使用的自定义域名来访问的，贴上相应的 `Nginx` 配置文件。

```bash
server {
    listen       80;
    server_name  code.pongj.com;

    rewrite ^(.*) https://$server_name$1 permanent;
}

server {
    listen 443 ssl;
    server_name code.pongj.com;
    ssl_certificate /etc/letsencrypt/live/code.pongj.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/code.pongj.com/privkey.pem;

    location / {
        proxy_pass http://localhost:8080/;
        proxy_set_header Host $host;
        proxy_set_header Upgrade $http_upgrade;
	proxy_set_header Connection upgrade;
	proxy_set_header Accept-Encoding gzip;
    }
}
```

我使用了 `lets encrypt` 来实现了全站的 `HTTPS` 证书配置和服务，下一篇文章我会详细介绍如何使用 `lets encrypt`  配置自己的证书文件。

## 资源

- GitHub 地址在[这里](https://github.com/cdr/code-server)

- `Service` 的配置教程在[这里](https://juejin.cn/post/6914579954487263239)