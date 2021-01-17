在之前的文章中简单介绍了 `parse-server` `parse-dashboard` 的安装和基本使用方法，如果还没看过的同学可以查看下面的文章链接预习一下哦

1. [快速上手基于 parse-server 的 Node.js 服务器使用篇之一](https://juejin.cn/post/6916396435013369863)
2. [快速上手基于 parse-server 的 Node.js 服务器使用篇之二](https://juejin.cn/post/6916792969962422286)
3. [使用 Parse 来管理你的 MongoDB 数据库](https://juejin.cn/post/6918346643843186702)

再本章节中我们主要学习一下如何使用 `Parse` 中的 `SDK` 使用方法，我再日常开发中使用 Node.js 比较多，因此 `SDK` 的示例就使用 JavaScript 来讲解

## 什么是 SDK

SDK（Software Development Kit）全称是软件开发工具包，也就是提供软件 API 接口或者使用方法的工具封装，能够让我们简单的使用 SDK 中暴露出来的方法进行日常的软件开发。

比如在小程序开发中如果使用图片上传的功能，我们就可以在项目中集成七牛云、又拍云或者腾讯云的文件存储 `OSS` SDK 来使用

## JavaScript 中的 SDK

在 JavaScript 中使用 `Parse`，官方团队提供了 JavaScript SDK，[传送门](https://github.com/parse-community/Parse-SDK-JS)。在 SDK 中，为我们提供了全局的 `Parse` 对象，对象中封装了 `parse-server` 中的数据的操作方式，下面我们来看看如何在项目中使用 `Parse`

### 配置安装

1. 在项目文件中安装 `parse` 模块

   ```
   yarn add parse
   ```

2. 在项目的根目录中添加 `parse.js`

   ```
   touch parse.js
   ```

3. 在 `parse.js` 中使用安装的 `parse` 模块

   `parse` 模块的执行环境分为了浏览器环境和 `Node.js` 环境，这是由于不同的运行环境中，`parse` 的权限操作不一样，因此我们在引用 `parse` 的时候需要引入不同的路径

   **模块引入**

   浏览器环境

   ```
   const Parse = require('parse');
   ```

   Node.js环境

   ```
   const Parse = require('parse/node');
   ```

   **配置**

   引入模块之后需要添加 `parse` 的服务器配置，配置的格式如下：

   ```
   Parse.initialize(APPID, JAVASCRIPT_KEY, MASTER_KEY)
   ```

   其中：

   -  `APPID` 是 `parse-server` 中的应用 ID
   - `JAVASCRIPT_KEY` 是 `parse-server` 中的 `JavaScript` 秘钥
   - `MASTER_KEY` 是 pa`rse-server` 中的超级管理员秘钥，请注意该秘钥只能在 Node.js 环境（服务器中）中使用，该秘钥应该避免暴露

   然后还需要配置 `parse-server` 的服务地址

   ```
   Parse.serverURL = 'http://localhost:1337/parse'
   ```

   添加 `serverURL` 的时候如果 `parse-server` 设置了服务挂载地址则一定不能缺少。

   在添加配置之后，作为可选项我们可以把 `Parse` 对象挂载在浏览器环境中的 `window` 对象中，或者在 Node.js 环境中挂载在 `global` 对象中，这样我们就不需要每次都加载 `parse.js` 了。

   **挂载对象**

   浏览器环境中

   ```
   window.Parse = Parse
   ```

   Node.js 环境中

   ```
   global.Parse = Parse
   ```

   而笔者在开发中，通常使用的是 Node.js 环境中，而且 Node.js 的模块默认开启了加载缓存，因此经常在需要的地方引入 `parse.js`

   下面是完整的 `parse.js` 文件内容

   ```
   const Parse = require('parse/node');
   
   Parse.initialize(process.env.PARSE_APPID, process.env.PARSE_JAVASCRIPT_KEY, process.env.PARSE_MASTER_KEY);
   Parse.serverURL = process.env.PARSE_SERVER_URL;
   
   module.exports = Parse;
   ```

   为了避免多次引入项目配置，我将所有的配置放在了环境变量中，以 `dotenv` 的方式来引入，想要了解以文件方式引入配置的同学可以看[这篇文章](https://juejin.cn/post/6854573213771366408)。

### 使用

在 SDK 中，开发者贴心的为我们分为了一下几个 `Parse` 的子对象，方便我们的使用，并且所有的 SDK 对象均返回 `Promise` 的结果，因此我们可以使用 `try catch` 来捕获错误。

下面是 SDK 中内置的对象：

- `Parse` ：暴露的顶级对象
- `Parse.Query`： `parse-server`中的查询 SDK，相当于 `GET` 的请求
- `Parse.Object`： `parse-server` 中的数据对象，相当于 `POST` 和 `PUT` 请求
- `ParseACL`：`parse-server` 中的权限控制对象，是 `Parse.Object` 的子对象继承
- `ParseFile`： `parse-server` 中的大文件管理对象，在 `MongoDB` 以 `GridFS` 引擎存储的 `raw` 文件
- `ParseUser`： `parse-server` 中的用户管理对象，是 `Parse.Object` 的子对象继承
- `ParseRole`： `parse-server` 中的角色组管理对象，是 `Parse.Object` 的子对象继承
- ...

还有几个不常用的内置对象，可以查看 SDK 的[文档地址](http://parseplatform.org/Parse-SDK-JS/api/2.19.0/Parse.Role.html)。

下面我们以常用的 `Parse.Query` 和 `Parse.Object`为例，介绍一下使用方法：

#### Parse.Query

示例 1：

查询一个列表中的数据

```
let result;
try {
	result = await new Parse.Query('Game').find();
} catch (error) {
	return console.error(error);
}
console.log(result);
```

  示例 2：

通过objectId 查询

```
let result;
try {
	result = await new Parse.Query('Game').get(objectId);
} catch (error) {
	return console.error(error);
}
console.log(result);
```

示例 3：

查询的时候分页，并对返回的结果进行排序

```
let result;
try {
	result = await new Parse.Query('Game').skip(10).limit(10).find();
} catch (error) {
	return console.error(error);
}
console.log(result);
```

代码块中的 `skip` 和 `limit` 是封装过的查询特定返回的数据

#### Parse.Object

示例 1：

在 `Game` 表中创建一条数据

```
let result;
try {
	result = await new Parse.Object('Game').save({
    "score": 1337,
    "playerName": "Sean Plott",
    "cheatMode": false,
    "createdAt": "2011-08-20T02:06:57.931Z",
    "updatedAt": "2011-08-20T02:06:57.931Z",
    "objectId": "Ed1nuqPvcm"
  });
} catch (error) {
	return console.error(error);
}
console.log(result);
```

通过使用 `save` 方法创建了一条数据

示例 2：

更改一条数据

使用 SDK 更改对象，需要进行 3 步操作：先查询对象，然后进行更改操作，最后在执行保存查询

```
// Query a game scope
let game;
try {
	result = await new Parse.Query('Game').first();
} catch (error) {
	return console.error(error);
}

// Change game score
game.set('score', 85);

let result;
try {
	result = await game.save();
} catch (error) {
	return console.error(error);
}
console.log(result);
```

从上面的代码中可以看到使用了 `set` 方法，这个是 `Parse.Object` 中的更改操作，能够更改指定字段的值。然后将更改过的对象保存到 `parse-server`

示例 3：

修改用户的用户名

```
// Query a game scope
let user;
try {
	result = await new Parse.Query('_User').first();
} catch (error) {
	return console.error(error);
}

// Change game score
user.set('username', 'mengj');

let result;
try {
	result = await user.save(null, {
    useMasterKey: true
	});
} catch (error) {
	return console.error(error);
}
console.log(result);
```

需要注意的是由于 `User` `Role` 等数据表是内置的并且属于写入限制的数据，因此我们需要在进行新建或者保存的时候需要使用到 `masterKey`，在 `save` 方法的第二个参数使用 `{ useMasterKey: true }` 来表示强制写入操作。

更多的示例和使用方法可以查看 SDK 的文档哦，虽然是英文，但是很好理解，[传送门](http://parseplatform.org/Parse-SDK-JS/api/2.19.0/Parse.Query.html)。

