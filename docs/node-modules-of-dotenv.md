# NODE_MODULES：项目中动态加载环境变量配置

## 开始

在平时的 `Node.js` 项目开发中，我们需要在项目中添加各种各样的配置：服务端口、服务地址、图片上传、数据库、`Redis` 等等。

通常情况下我们会在项目的根目录中添加`config.js`或者`config.json`配置文件，然后在需要使用到的时候，在项目中`require`配置文件。

```js
module.exports = {
  SERVER: 'https://www.example.com',
  PORT: 7890,
  REDIS: '127.0.0.1:6379',
  MONGODB: 'mongodb://127.0.0.1:27017/example'
}
```

<p align="right">config.js</p>

通过使用配置文件在大部分情况下比较简单明了，但是如果有多个配置环境的时候就有一些棘手，有的同学会说可以通过判断`NODE_ENV`是否为`production`来判断是否使用生产环境配置，那样的话就需要在项目运行的时候在命令行中添加

```bash
NODE_ENV=production node index.js
```

现在我们可以通过使用`dotenv`的第三方包，在项目的根目录中添加`.env`的配置文件，就可以在项目中使用`process.env`的环境变量配置

## 使用

### 基本使用

`dotenv`就是一个可以通过加载配置文件，将配置动态添加到 `Node.js` 的执行环境变量中。

首先我们在项目中添加`.env`配置文件

```bash
NODE_ENV=production
SERVER=https://www.example.com
PORT=7890
REDIS=127.0.0.1:6379
MONGODB=mongodb://127.0.0.1:27017
```

<p align="right">.env</p>

然后在我们的项目入口文件中使用`dotenv`加载配置

```js
// index.js
const config = require('dotenv').config();
const http = require('http');

const port = process.env.PORT || '1337';
const server = http.createServer((rea, res) => {
  res.status(200).send('Server is Running')
})
server.listen(port);
```

或者我们也可以在命令行启动的时候添加

```bash
node -r dotenv/config index.js
```

添加之后我们就可以在整个项目中使用 `process.env`来使用环境变量的配置了。

### 高级使用

#### 解析状态

我们可以在加载配置文件之后添加配置文件的加载是否成功的判断，如果加载的过程中出现错误，解析之后 `config.error`包含了加载的错误信息。`config.parsed`包含了解析成功的配置参数，在脚本操作中使用 `config.parsed`是比较方便的。

```js
const config = dotenv.config()

if (config.error) {
  throw config.error
}

console.log(config.parsed)
```

#### 更改配置文件

如果我们不想使用`.env`配置文件，想使用`.config`或者类似于`vue-cli`的环境变量文件配置`.env.production`，我们可以在加载配置文件的时候将配置文件名称更改即可

```js
// index.js
const config = require('dotenv').config({ path: '/custom/path/to/.env.production' })
const http = require('http');

const port = process.env.PORT || '1337';
const server = http.createServer((rea, res) => {
  res.status(200).send('Server is Running')
})
server.listen(port);
```

更多的内容可以查看模块的[地址](https://github.com/motdotla/dotenv)

## 参考

- Github 地址：[motdotla](https://github.com/motdotla)/**[dotenv](https://github.com/motdotla/dotenv)**

- NPM地址：[dotenv](https://www.npmjs.com/package/dotenv)

## 更多

更多的内容请关注[GitHub](https://github.com/hom)，知乎@[大手印](https://www.zhihu.com/people/meng-jun-23-36)，或者关注我的公众号@全栈开发师，我会不定时分享一些全栈方向的开发内容，谢谢分享

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ggms66rig8j31bi0hcgol.jpg)

