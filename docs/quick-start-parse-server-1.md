作为一名合格的全栈开发者，如果没有自己的南拳七式，都不好意思说是 `Node.js` 全栈开发师。在过去的 2020 年中前端中各大厂商都推出了自己的云开发和 `Serverless`、以及各种各样的微服务席卷一地，今天我要介绍的是一个价值 `85,000,000` 美金的 `Node.js` 微服务的服务器。

![parse-dashboard](https://cdn.pongj.com/uPic/20210111-1610349402187-YZoSp4.png)

## 什么是 Parse

Parse，它是在 2013 年被 `Facebook` 以 8500 万美金收购的项目，在 Facebook 中孕育了 4 年之后于 2017 年 02 月 28 日在 `GitHub` 中开源，查看该项目的[地址](https://github.com/parse-community/parse-server)。

`parse` 的服务包含了后台服务 [`parse-server`](https://github.com/parse-community/parse-server)，后台管理台 [`parse-dashboard`](https://github.com/parse-community/parse-dashboard) ，`iOS` `OSX` `tvOS` 的 SDK 插件 [`parse-sdk-ios-osx`](https://github.com/parse-community/Parse-SDK-iOS-OSX)，`JavaScript` 的 SDK 插件 [`parse-sdk-js`](https://github.com/parse-community/Parse-SDK-JS)，以及 `Android` 、`PHP` 等其他语言的插件

今天手把手搭建一个 `parse-server` 的服务，以及包含一个完整的数据库管理台，让你快速上手实现一个完整的后台服务

## 安装

### 前提条件

- Node.js

  Node.js 的版本需要 >= 8

- MongoDB

  MongoDB 的版本需要 >= 3.0，如果没有安装 `MongoDB`，则至少需要一个安装了 MongoDB 的服务器以及诸如 `mongodb://host:ip` 的链接地址

  

安装 `parse-server` 和 `parse-dashboard`

```bash
yarn global parse-server parse-dashboard

# or use npm
npm install -g parse-server parse-dashboard
```

安装完成之后查看全局安装的可执行文件是否执行正常

```bash
# test parse-server
parse-server --help

# test parse-dashboard
parse-dashboard --help
```



## 配置

安装完成之后我们需要了解 `parse-server` 的运行方式以及配置文件的格式，`parse-server` 可以在命令行中通过添加参数的方式或者配置文件的方式运行。

一个`parse-server` 服务至少需要以下参数：

- `appId` 一个服务的应用 ID，用来标识不同的应用程序以及在 API 请求中的识别
- masterKey 应用程序中权限最大的秘钥，能够全局读写 `parse-server` 服务，因此该秘钥必须配置并且需要谨慎使用
- `databaseURI`  数据库的地址。目前 `parse-server` 支持 MongoDB 和 `PostgreSQL`
- `javascriptKey` `JavaScript` SDK 的请求秘钥，如果使用SDK 则必须配置
- `resAPIKey` 使用 REST API 请求必须使用的秘钥



1. 命令行中启动

然后可以通过在命令行中执行以下命令快速启动 `parse-server`

```bash
parse-server --appId APPLICATION_ID --masterKey MASTER_KEY --databaseURI mongodb://localhost:27017/test

# output
allowClientClassCreation: true
appId: APPLICATION_ID
cacheMaxSize: 10000
cacheTTL: 5000
customPages: {}
databaseURI: mongodb://localhost:27017/test
enableAnonymousUsers: true
expireInactiveSessions: true
graphQLPath: /graphql
host: 0.0.0.0
idempotencyOptions: {"ttl":300,"paths":[]}
logsFolder: ./logs
masterKey: ***REDACTED***
masterKeyIps: []
maxUploadSize: 20mb
mountPath: /parse
objectIdSize: 10
playgroundPath: /playground
port: 1337
protectedFields: {"_User":{"*":["email"]}}
revokeSessionOnPasswordReset: true
schemaCacheTTL: 5000
sessionLength: 31536000
allowCustomObjectId: false
collectionPrefix:
directAccess: false
enableExpressErrorHandler: false
enableSingleSchemaCache: false
mountGraphQL: false
mountPlayground: false
preserveFileName: false
preventLoginWithUnverifiedEmail: false
scheduledPush: false
verifyUserEmails: false
jsonLogs: false
verbose: false
level: undefined
serverURL: http://localhost:1337/parse
```

在成功执行之后，`parse-server` 会输出我们的服务器的配置，默认的服务端口是 `1337`， 如果需要更改端口可以在执行的时候添加 `--port` 参数。

2. 配置文件的方式

   添加配置文件 `parse-server.config.json`

   ```bash
   {
     "appId": "MYAPPID",
     "masterKey": "MYMASTERKEY",
     "restAPIKey": "MYRESTAPIKEY",
     "databaseURI": "mongodb://localhost:27017/example",
     "port": 1337,
     "serverURL": "http://localhost:1337"
   }
   ```

   然后在命令行中进入到文件目录并执行

   ```bash
   parse-server parse-server.config.json
   ```

   `parse-server` 的服务就在 `localhost:1337` 中启动成功

3. 运行

   然后在浏览器中输入 `localhost:1337/parse/health` 即可看到`parse-server`  的返回结果

   ```bash
   {"status":"ok"}
   ```

   ![](https://cdn.pongj.com/uPic/20210111-1610349558651-NPLZHn.png)

## 服务

通常我们使用 `parse-server` 作为单独的服务或者作为 `express` 的中间件运行。

以下是两种服务方式的说明：

1. 单独的服务

   作为单独的服务可以在后台通过和 `Nginx` 配合实现负载均衡，或者使用 `Docker` 部署，与自定义的服务之间可以通过 REST 请求或者使用 SDK 进行请求

2. 中间件方式

   以中间件的方式可以通过挂载的方式使用 `/parse` 或者其他参数来访问服务，好处是能够与自定义的业务集成在一个服务中，内部访问比较快。缺点就是占用了路由的地址，如果有大量的自定义路由会造成混淆

我在项目中通常使用中间件方式的运行，因此 `parse` 官方提供了以中间件运行的[模板项目](https://github.com/parse-community/parse-server-example)，我自己在使用的时候添加了自定义的配置文件方式，以及路由的挂载，如果你有兴趣也可以基于我的 `Github` 的https://github.com/hom/parse-server-template

使用我的 `parse-server` 模板可以参考一下步骤

1. 基于 `.env.example` 文件添加 `.env` 配置文件

```bash
cat .env.example > .env
```

2. 添加 `.env` 文件的配置值

3. 安装项目的依赖

   ```bash
   # install packages
   yarn install
   # then
   yarn run start
   ```

下一篇内容我将详细介绍 关于 `parse-server` 的使用以及管理台的配置和安装