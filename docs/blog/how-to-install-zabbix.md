# Zabbix安装

## Zabbix Server 安装

1. 下载包

```bash
rpm -ivh http://repo.zabbix.com/zabbix/3.4/rhel/7/x86_64/zabbix-release-3.4-2.el7.noarch.rpm
```

2. 安装zabbix

```bash
yum install -y zabbix-server-mysql zabbix-get zabbix-web zabbix-web-mysql zabbix-agent zabbix-sender
```

3. 数据库初始化

```bash
# 进入mysql命令行
mysql -uroot -p

# 创建zabbix数据库
create database zabbix character set utf8 collate utf8_bin;

# 创建zabbix账户
grant all privileges on zabbix.* to zabbix@localhost identified by 'zabbix';

# 执行刷新
flush privileges;

# 退出
quit
```

进入`zabbix`安装目录

```bash
# 进入zabbix目录
cd /usr/share/doc/zabbix-server-mysql-3.4.2/

# 解压
gunzip create.sql.gz

# 导入表
mysql

use zabbix;

source create.sql;

```

4. 配置zabbix server配置文件

```bash
cd /etc/zabbix

cp zabbix_server.conf zabbix_server.conf.backup

vim zabbix_server.conf
```

添加配置

```bash
DBHost=localhost

DBName=zabbix

DBUser=zabbix

DBPassword=zabbix

# DBSocket=/tmp/mysql.sock
DBSocket=/var/lib/mysql/mysql.sock
```

运行zabbix-server服务

```bash
systemctl start zabbix-server
```

5. 配置php

```bash
cd /etc/httpd/conf.d/

cp zabbix.conf zabbix.conf.backup

vim zabbix.conf
```

配置时区

```bash
# php_value date.timezone Europe/Riga
php_value date.timezone Asia/Shanghai
```

重启`httpd`

```bash
systemctl restart httpd
```

6. 连接zabbix前端页面即可

<p align="center">
  <a href="http://www.beian.miit.gov.cn">豫ICP备18042292号-2</a>
</p>