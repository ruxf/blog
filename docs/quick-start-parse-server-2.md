在[上一篇内容](https://juejin.cn/post/6916396435013369863)中简单介绍了什么是 `parse-server` ，以及 `parse-server` 的简单使用，本节中我们将学习到以下内容：

- `parse-dashboard` 的使用和配置
- `parse-dashboard` 的界面简介

## 什么是 parse-dashboard

`parse-dashboard` 是 `Parse` 团队开发的 `parse-server` 数据库图形化使用界面，相比较 `Robomongo` 等其他 `MongoDB` 的图形化数据库操作工具，`parse-dashboard`  主要为网站管理员提供了一个可配置化的 `MongoDB` 数据操作工具。

`parse-dashboard` 的定位是介于系统的 CMS 系统和数据库的原始数据管理工具之间，工具内部集成了 `parse-server` 的 API 接口以及 Master 用户权限；同时 `parse-dashboard` 对 `Parse` 自定义的数据格式（`Pointer`、`Relation` 等）进行了处理，从而能够更直观的展示和操作数据集

下面的是原始的 `Robomongo` 工具的界面

![robomongo](https://cdn.pongj.com/uPic/20210112-1610434561790-BKPBB7.png)

下图所示的是 `parse-dashboard` 的界面

![parse-dashboard](https://cdn.pongj.com/uPic/20210111-1610349402187-YZoSp4.png) 

`parse-dashboard` 和 `Robomongo` 的最大不同之处在于工具背后的连接主体不同，下图展示了两款工具的连接使用方式

![](https://cdn.pongj.com/uPic/20210112-1610435467354-8EcJQE.png)

## 配置

`parse-dashboard` 的配置比较简单，一个实例必须以下参数

- `appName` 管理台中显示的应用名称

- `appId` `parse-server` 的应用 ID
- `masterKey` parse-server 的超级管理员秘钥
- `serverURL` `parse-server` 的服务地址

可选

- 登录用户的密码

1. 命令行使用

   在安装 `parse-dashboard` 之后，我们可以通过执行 `--help` 查看使用帮助

   ```bash
   npm install -g parse-dashboard
   
   # Test
   parse-dashboard --help
   ```

   在执行 parse-dashboard 之前必须首先启动 `parse-server`，然后在命令行中就可以使用添加参数的方式或者添加环境变量的方式来启动 `parse-dashboard`

   ```bash
   parse-dashboard --dev --appId MYAPPID --masterKey MYMASTERKEY --serverURL "http://localhost:1337" --appName MYAPP
   
   # 默认的地址是 http://localhost:4040
   # output
   The dashboard is now available at http://0.0.0.0:4040/
   
   ```

   

2. 配置文件使用

   除了可以在命令行中添加参数或者环境变量的方式启动，`parse-dashboard` 也可以通过读取配置文件的方式来启动

   新建 `dashboard.config.json`

   ```json
   {
     "apps": [
       {
         "serverURL": "http://localhost:1337/parse",
         "appId": "MYAPPID",
         "masterKey": "MYMASTERKEY",
         "appName": "Example"
       }
     ]
   }
   
   ```

   然后在文件的目录下通过添加 `--config` 参数来指定配置文件

   ```bash
   parse-dashboard --config dashboard.config.json
   ```

3. 然后在浏览器中输入 `localhost:4040`，如果可以看到 `parse-dashboard` 的界面，就说明我们的操作是正确的。

   ![parse-dashboard](https://cdn.pongj.com/uPic/20210112-1610437785680-s14u5k.png)

## 使用

启动成功的 `parse-dashboard` 默认会显示 `parse-server` 的 `User` 和 `Role` 数据库，`Role` 和 `User` 集成的默认创建的数据库，包含了一些默认的字段和配置，同时作为这个应用的用户管理和角色管理基础功能，比如 `User` 中的 `username` 和 `email` 就作为登录使用的用户名，`password` 默认是隐藏的

### Role

![Role](https://cdn.pongj.com/uPic/20210112-1610437830232-xYyygh.png)

默认的字段包括：

- `objectId` `Parse` 包装过的数据的主键，数据类型为 `String`
- `createdAt` 数据的创建时间，数据类型为 `Date`
- `updatedAt` 数据的更新时间，数据类型为 `Date`
- `ACL` 全称为 `Access Control List` ，代表该条数据的访问权限。这也是 `parse-server` 逆天的一个功能，每一个用户的权限能够具体到每一个表中的每一条数据，后面我们会详细介绍的。
- `name` 该角色组的名称，全局唯一不重复，数据类型为 `String`
- `users` 该用户组内的用户，数据类型为 `Relation`，后面我们详细介绍 `Relation` 和 `Pointer` 的类型

- `roles` 该用户组所包含的其他用户组，通常用于权限继承

`Role` 在用户的基础上添加了用户组的概念，通常适用于多个人具有相同的读取权限，并且能够实现权限继承，很酷有没有？笔者曾经使用用户和用户组实现了公司全国各地 `Zabbix` 资源池的监控，用户权限级别包含了 `公司` `省` `市 ` `资源池` 的各级`管理员` `运维` `用户` 的权限分级，并且运行良好，关于 `Role` 的权限分级在下面的内容中以通俗的示例讲解哈

### User

用户是 Parse 中用户的表，其中包含了 `session` `password` 等隐藏显示的字段，以及 `username` `email` 等全局唯一标识用户的字段

![User](https://cdn.pongj.com/uPic/20210112-1610438606038-jpRhzU.png)

默认的字段包括：

- `objectId` 主键
- `mailVerified` `Boolean` 类型，是否启用邮箱验证功能
- `ACL` `ACL`类型，数据的访问权限
- `updatedAt` `Date` 类型，更新时间
- `createdAt` `Date` 类型，创建时间
- `authData` `Object` 类型，第三方的验证数据
- `username` `String` 类型，全局唯一的用户名
- `password` `String` 类型，默认隐藏显示的密码
- `email` `String` 类型，用户的邮箱

用户的默认表结构基本就是这样，如果有自定义的字段，可以通过管理台添加或者通过创建用户的时候添加相应的字段数据，`Parse` 就可以按照数据的格式添加一个相应类型的字段。

### 示例

我们可以通过简单的网络请求创建一条数据，然后在 `parse-dashboard` 中查看我们的创建结果

新建一个名为 `Game` 表格并创建一条数据，需要注意的是在请求头中需要添加 `parse-server` 的配置 `Header`：`X-Parse-Application-Id` `X-Parse-REST-API-Key`

```bash
  curl -X POST \
  -H "X-Parse-Application-Id: MYAPPID" \
  -H "X-Parse-REST-API-Key: MYRESTAPIKEY" \
  -H "Content-Type: application/json" \
  -d '{"score":1337,"playerName":"Sean Plott","cheatMode":false}' \
  http://localhost:1337/parse/classes/Game
```

网络请求返回的结果包含了操作的结果，说明我们的创建请求成功处理了。

```bash
{"objectId":"ncuoD5hIfw","createdAt":"2021-01-12T08:36:56.846Z"}
```

然后我们在管理台中刷新可以看到一个新的表 `Game` 和一条数据包括每个字段的类型均已展示出来了。

![Create-a-class](https://cdn.pongj.com/uPic/20210112-1610440862788-SR6hqL.png)

更多的内容和用法可以查看官方的 API [文档](http://docs.parseplatform.org/rest/guide/)

