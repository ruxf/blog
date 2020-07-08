# 服务器：走向全栈开发的第一步

现在前端脚手架和框架语言给前端开发者带来更多的便利，在开发之余可能更想去了解一些开发的知识，而对于前端开发，熟悉`Node.js`以及后端的一些数据库等会是一些比较容易上手的方式。拥有一台服务器是一件很酷的事情，所以分享一些常用的服务器软件的安装，以下的服务器默认是`CentOS`系统。

## 开发必备

### Node.js

前端接触最多的后台语言当然是`Node.js`了，所以在服务器上需要安装`Node.js`。在正常的情况下官方会建议我们使用源码去编译安装，但是源码安装的话后续想要升级或者更改版本就需要重新编译，所以我们使用`Node.js`的版本管理神器`nvm`。

安装 `nvm` 很简单使用[安装脚本](https://github.com/nvm-sh/nvm)

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash
```

或者

```bash
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash
```

安装完成后可以断开服务器连接，重新登录上就可以了，如果不想重新登录也是可以的，在根目录下执行

```bash
export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
```

就可以了，然后我们可以使用`nvm`安装任一版本的 `Node.js`

```bash
# 显示 lts 版本的 Node.js
nvm ls-remote --lts
# 安装 Erbium 版本的 Node.js
nvm install --lts=Erbium
# 或者安装最新版本
nvm install stable
```

安装完成可以查看安装的 `Node.js`版本

```bash
node --version
> v14.5.0
```

如果在下次登录服务器的时候显示 `nvm`报错如下

```bash
N/A: version "N/A -> N/A" is not yet installed.
```

是由于`nvm`默认的`Node.js`版本未指定，所以我们需要指定默认的`Node.js`版本

```bash
# nvm alias default [想要指定的默认版本]
nvm alias default node
```

### Yarn

> `yarn`是新一代的`Node.js`包管理工具，它可以缓存下载的每一个包，它还能并行化操作以最大化资源利用率，安装速度之快前所未有。

安装`yarn`很简单，首先添加源地址

```bash
curl --silent --location https://dl.yarnpkg.com/rpm/yarn.repo | sudo tee /etc/yum.repos.d/yarn.repo
```

如果没有安装`Node.js`，需要先安装`Node.js`

```bash
curl --silent --location https://rpm.nodesource.com/setup_12.x | sudo bash -
```

然后执行安装的命令就可以了

```bash
sudo yum install yarn
## OR ##
sudo dnf install yarn
```

如果想要查看其它的安装方式，可以查看[官网](https://classic.yarnpkg.com/en/docs/install#mac-stable)

### Git

自从`GitHub`被微软收购以后，开放了个人私有库以及持续集成插件，现在不会使用`Git`就有点不好意思说出口了哈哈

安装 `Git`，之前我也是通过源代码模式来编译安装的，后来发现可以使用`rpm`包来安装，并且可以随时升级和删除，所以我推荐使用包管理来安装。

首先就是添加`repo`源

```bash
yum install -y http://opensource.wandisco.com/centos/7/git/x86_64/wandisco-git-release-7-2.noarch.rpm
```

添加之后就可以在`/etc/yum.repos.d/wandisco-git.repo`看到安装的`git`源，然后执行安装

```bash
yum install git -y
```

查看安装的版本

```bash
git --version
```

更新`git`版本

```bash
yum upgrade git
```

### MongoDB

作为后台开发，`mongodb`自然是少不了的，在现在数据结构多样化，`NoSQL`能够大大方便数据库结构的设计和存储

首先添加`mongodb`源

```bash
vim /etc/yum.repos.d/mongodb-org-3.6.repo
```

配置`yum`源内容

```bash
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
```

安装

```bash
sudo yum install -y mongodb-org
```

启动`mongodb`，默认的配置文件在`/etc/mongod.conf`，数据存储位置在`/var/run/mongodb`，如果有其他需求可以更改数据库位置

```bash
systemctl start mongod
```

启动成功我们就可以使用`mongo`来连接到数据库

### Mariadb

>  `mariadb`是`mysql`的开源替代版本，使用方式和`mysql`是一样的

1. 新建`Mariadb.repo`源文件

   ```bash
   vim /etc/yum.repos.d/Mariadb.repo
   ```

2. 写入内容

   ```bash
   name = MariaDB
   baseurl = http://yum.mariadb.org/10.4/centos7-amd64
   gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
   gpgcheck=1
   ```

3. 安装

   ```bash
   yum -y install MariaDB-server MariaDB-client
   ```
   
4. 启动

   ```bash
   # 启动
   systemctl start mariadb
   # 设置开机启动
   systemctl enable mariadb
   # 重启
   systemctl restart mariadb
   ```

### Nginx

想要充分发挥后端服务的能力，`Nginx`少不了，`Nginx`能够实现静态资源代理，也可以反向代理服务器，从而能够提高服务的健壮性

#### 安装`Nginx`

1. 安装`yum-utils`
```bash
yum install -y yum-utils
```

2. 添加`yum`源。

```bash
vim /etc/yum.repos.d/nginx.repo
```

内容如下：

```bash
[nginx-stable]
name=nginx stable repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=1
enabled=1
gpgkey=https://nginx.org/keys/nginx_signing.key
module_hotfixes=true

[nginx-mainline]
name=nginx mainline repo
baseurl=http://nginx.org/packages/mainline/centos/$releasever/$basearch/
gpgcheck=1
enabled=0
gpgkey=https://nginx.org/keys/nginx_signing.key
module_hotfixes=true
```
3. 执行安装命令
```bash
yum install -y nginx
```
4. 查看`nginx`版本
```bash
#查看nginx版本
nginx -v
#查看编译参数
nginx -V
```
5. 查看安装目录
```bash
rpm -ql nginx
```
6. 查看配置文件
```bash
# 用于日志切割
/etc/logrotate.d/nginx
```

#### 配置`Https`

在前后端交互过程中，使用`https`请求是很有必要的，但是开启`https`需要根据域名来配置，

，所以需要购买或者使用解析到服务器的域名。

有了域名还不行，需要有证书才能开启`https`，有的同学可能说了，可以自己通过`openssl`来自签名证书，这样也是可以的，但是在一些浏览器还是会显示不安全证书，所以为了浏览器能够在我们的网站中显示安全的锁，我们需要使用权威机构的证书来操作，当然是免费的了，我们使用`letsencrypt`，是全球权威的自签名 证书，证书的有效期是 6 个月，证书到期可以续期。

使用`letsencrypt`不得不说到`certbot`，它是完美支持`letsencrypt`的命令行工具，通过`certbot`可以签名、续期证书，官网地址请看[这里](https://certbot.eff.org/)，在官网中可以通过选择`https`要运行的软件和系统，来提供安装的说明。以下是基于`Nginx`和`CentOS`来安装的`certbot`

1. 需要确定`Nginx`是运行状态，域名已经解析到服务器的`IP`

   ```bash
   # 查看 Nginx 运行状态
   systemctl status nginx
   # 查看域名的解析地址
   dig www.example.com
   ```

2. 安装`certbot`

   ```bash
   sudo yum install certbot python2-certbot-nginx
   ```

3. 签名域名

   ```bash
   sudo certbot certonly --nginx --register-unsafely-without-email
   ```

4. 签名完成后就可以在`Nginx`中开启`https`的证书了，证书的位置在`/etc/letsencrypt/live/`目录中，证书的文件夹名称为签名的域名，以下是在`Nginx`中配置`https`的配置文件实例

   ```bash
   upstream copybook_pongj {
       server localhost:35231;
   }
   
   server {
       listen       80;
       server_name  copybook.pongj.com;
   
       rewrite ^(.*) https://$server_name$1 permanent;
   }
   
   server {
       listen 443 ssl;
       server_name copybook.pongj.com;
       ssl_certificate /etc/letsencrypt/live/copybook.pongj.com/fullchain.pem;
       ssl_certificate_key /etc/letsencrypt/live/copybook.pongj.com/privkey.pem;
   
       location / {
           try_files $uri $uri/ /index.html;
           root   /data/pongj/copybook-web/dist/;
           index  index.html index.htm;
       }
   
       location ~ /(parse|api|public) {
           proxy_pass  http://copybook_pongj;
           # Proxy setting
           proxy_redirect     off;
           proxy_set_header   Host             $host:80;
           proxy_set_header   X-Real-IP        $remote_addr;
           proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
       }
   
       #error_page  404              /404.html;
   
       # redirect server error pages to the static page /50x.html
       #
       error_page   500 502 503 504  /50x.html;
       location = /50x.html {
           root   /usr/share/nginx/html;
       }
   }
   ```

   在示例文件中使用了反向代理，将业务服务器和资源文件通过`location`分离开来，在默认的`http`请求中，将`http`请求重定向到`https`请求处理，通过`listen 443 ssl`开启`https`

## 其他工具

### docker

#### 源安装

经常关注云计算方面的同学可以了解，容器技术现在比较火热，所以我们可以在服务器中部署自己的`docker`服务，配合`docker-compose`，以及`kubernetes`搭建自己的练手后花园

卸载旧版本

```bash
sudo yum remove docker \
docker-common \
docker-selinux \
docker-engine
```

安装依赖包

```bash
sudo yum install -y yum-utils \
device-mapper-persistent-data \
lvm2
```

添加`yum源`

```bash
# 官方源
# sudo yum-config-manager \
#--add-repo \
#https://download.docker.com/linux/centos/docker-ce.repo

# 建议使用国内源
sudo yum-config-manager \
--add-repo \
https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

安装最新版本的`docker-ce`

```bash
sudo yum-config-manager --enable docker-ce-edge
```

安装测试版本的`docker-ce`

```bash
sudo yum-config-manager --enable docker-ce-test
```

安装稳定版本
```bash
sudo yum makecache fast
sudo yum install docker-ce docker-ce-cli containerd.io
```

配置镜像加速

linux

```bash
curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://f1361db2.m.daocloud.io
```

macos

右键点击桌面顶栏的 docker 图标，选择 Preferences ，在 Daemon 标签（Docker 17.03 之前版本为 Advanced 标签）下的 Registry mirrors 列表中加入下面的镜像地址:

```bash
$ http://f1361db2.m.daocloud.io
```

安装完成之后重启`docker`即可

```bash
sudo systemctl restart docker
```

#### 脚本安装

```bash
curl -fsSL https://get.docker.com/ | sh
# or
wget -qO- https://get.docker.com/ | sh
```

### docker-compose

1. 下载`docker-compose`执行文件

   ```bash
   sudo curl -L "https://github.com/docker/compose/releases/download/1.26.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
   ```

2. 添加执行权限

   ```bash
   sudo chmod +x /usr/local/bin/docker-compose
   ```

3. 如果安装完成执行命令后找不到`docker-compose`，需要手动添加文件链接

   ```bash
   sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
   ```

安装完成后就可以通过`docker-compose`开启多个容器互联了

## 更多内容

更多的内容请关注[GitHub](https://github.com/hom)，或者关注我的公众号@全栈开发师，我会不定时分享一些全栈方向的开发内容，谢谢分享