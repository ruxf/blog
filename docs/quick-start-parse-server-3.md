在上一篇文章中，我们介绍了如何安装和简单使用 `parse-dashboard` 以图形化的页面来管理我们的 `parse-server` 服务。在系统开发中，通常我们需要根据产品的服务和形态来定义我们的数据库表结构，在使用 MySQL 中，我们通常以文件中数据库初始化命令的形式来初始化数据库，以及表结构和字段，而使用 MongoDB 定义好的表结构则通常没有那么简单，在本章节中我们来手把手学习通过 `parse-dashboard` 来定义我们的数据库结构以及如何进行数据库初始化。

## 设计表结构

### 确定字段值

通常我们在接到新的需求之后会对需求进行分析，然后开始设计表结构，现在我们假设需要设计一个用户数据表，存储用户的基本信息：

其中用户表中的字段包括：

| 字段名称 | 数据类型 | 是否必填 | 默认值 |
| -------- | -------- | -------- | ------ |
| username | String   | 是       | Null   |
| password | String   | 是       | Null   |
| avatar   | String   | 否       | Null   |
| phone    | String   | 是       | Null   |
| email    | String   | 是       | Null   |
| city     | String   | 否       | Null   |
| country  | String   | 否       | Null   |
| age      | Number   | 否       | 0      |
| gender   | Number   | 否       | 0      |

确定好数据表中的字段名称、类型之后，我们就可以结合表结构在 `parse-dashboard` 中的添加字段值了。

### 操作示例

1.  进入到 `parse-dashboard` 中的`User` 表中

   ![Snipaste_2021-01-16_20-00-37](https://cdn.pongj.com/uPic/20210116-1610799734021-LlT37T.png)

2. 点击列表顶部中的 `Edit`，然后再点击页面中间的 `Add a column` 的按钮

   ![](https://cdn.pongj.com/uPic/20210116-1610799921154-jojcNb.png)

   

   

3. 在弹出的操作框中填入字段的名称，以 `phone` 为例：

   ![](https://cdn.pongj.com/uPic/20210116-1610800091735-QwRVvb.png)

   添加字段的类型为 `String`， 由于在 JavaScript 中使用 String 类型要占用的存储空间比较小，因此此处的类型选择 String 更为合适；在字段的名称中填入 `phone`，在 `required` 选项中选择为 `Yes`

4. 添加完成字段之后我们可以点击页面中的 `Add a row` 来手动添加一条数据来测试一下

   ![Snipaste_2021-01-16_20-00-37](https://cdn.pongj.com/uPic/20210116-1610799734021-LlT37T.png)

   然后填入默认已经添加的字段 `username` 、`password` 以及我们自定义的字段 `phone`，然后就可以看到我们已经添加的这条数据，以及在表头中显示了该列数据的数据格式是 `String`

   ![](https://cdn.pongj.com/uPic/20210116-1610800460885-pfZSM9.png)

其他的几个字段也可以按照上面的操作创建到 MongoDB 数据库。

### Pointer 类型

这样我们就可以通过在 `parse-dashboard` 的管理台中使用可视化的方式来创建我们的表数据格式，接下来我们可以在上一节中创建的 `Game` 表中添加一个 `user` 的字段，声明类型值为 `Pointer` 指向 `User` 表

1. ![](https://cdn.pongj.com/uPic/20210116-1610800706368-wdGXQ0.png)

   然后我们可以看到在 `Game` 表中的 `user` 字段默认为 `undefined`

   ![](https://cdn.pongj.com/uPic/20210116-1610800774235-H2FAiO.png)

2. 点击 `Game` 中第一条数据的 `user` 表格，就可以将表格变更为一个输入框，输入我们已经创建的用户的 `objectId`

   ![](https://cdn.pongj.com/uPic/20210116-1610800943250-RK3SWt.png) 

3. 移开鼠标之后，表格的内容将自动保存到 `parse-server` 的数据库了，同时该表格已经展示为一个链接按钮，点击之后可以直接进入到 `User` 表中的该条用户数据，这在日常开发中能够为多个数据表的外联添加了方便的可操作性，能够方便的查看某一条数据所对应的其他表中的数据，这就是 `parse-server` 中支持的数据类型 `Pointer` 的魅力之处，同时 `parse-dashboard` 又将该类型做了优化展示

   ![](https://cdn.pongj.com/uPic/20210116-1610801177229-q1x57b.png)

## 结构输出

在 `parse-dashboard` 中使用鼠标点点就可以创建了表结构，那我们怎么查看创建的表的原始数据结构呢？我们可以通过 MongoDB 的命令行工具查看表的解构类型。

1. 进入到 MongoDB 的交互式命令行中

   ```bash
   mongo
   ```

2. 查看数据库列表

   ```bash
   use test;
   
   show tables;
   ```

   输出

   ```bash
   > use test
   switched to db test
   > show tables
   Game
   _Idempotency
   _Role
   _SCHEMA
   _Session
   _User
   >
   ```

   可以从数据表中看到 `parse-server` 的数据库默认多了几个以下划线开始的表：

   - `_Idemotency` 数据库的索引值
   - `_Role` 角色表，即在 `parse-dashboard` 中看到的 `Role` 数据表
   - `_SCHEMA` 所有数据表的格式定义，也就是本节中的重要内容
   - `_Session` 用户的登录 `session` 表，标识用户的登录状态
   - `_User` 用户表，即在 `parse-dashboard` 中看到的 `User` 数据表

3. 查看数据表的格式定义 `_SCHEMA`

   ```
   db.getCollection('_SCHEMA').find().pretty()
   ```

   由于 `_SCHEMA` 使用了下划线开头，因此我们需要使用 MongoDB 提供的 `getCollection` 方法来获取到表的实例

   **输出：**

   ```
   {
   	"_id" : "_User",
   	"objectId" : "string",
   	"updatedAt" : "date",
   	"createdAt" : "date",
   	"username" : "string",
   	"email" : "string",
   	"emailVerified" : "boolean",
   	"authData" : "object",
   	"_metadata" : {
   		"indexes" : {
   			"_id_" : {
   				"_id" : 1
   			},
   			"username_1" : {
   				"username" : 1
   			},
   			"email_1" : {
   				"email" : 1
   			},
   			"case_insensitive_username" : {
   				"username" : 1
   			},
   			"case_insensitive_email" : {
   				"email" : 1
   			}
   		},
   		"fields_options" : {
   			"phone" : {
   				"required" : true,
   				"defaultValue" : "null"
   			}
   		}
   	},
   	"phone" : "string"
   }
   {
   	"_id" : "_Role",
   	"objectId" : "string",
   	"updatedAt" : "date",
   	"createdAt" : "date",
   	"name" : "string",
   	"users" : "relation<_User>",
   	"roles" : "relation<_Role>",
   	"_metadata" : {
   		"indexes" : {
   			"_id_" : {
   				"_id" : 1
   			},
   			"name_1" : {
   				"name" : 1
   			}
   		}
   	}
   }
   {
   	"_id" : "Game",
   	"objectId" : "string",
   	"updatedAt" : "date",
   	"createdAt" : "date",
   	"score" : "number",
   	"cheatMode" : "boolean",
   	"playerName" : "string",
   	"_metadata" : {
   		"indexes" : {
   			"_id_" : {
   				"_id" : 1
   			}
   		},
   		"fields_options" : {
   			"user" : {
   				"required" : true
   			}
   		}
   	},
   	"user" : "*_User"
   }
   {
   	"_id" : "_Session",
   	"objectId" : "string",
   	"updatedAt" : "date",
   	"createdAt" : "date",
   	"restricted" : "boolean",
   	"user" : "*_User",
   	"installationId" : "string",
   	"sessionToken" : "string",
   	"expiresAt" : "date",
   	"createdWith" : "object"
   }
   ```

   可以看到表的数据中的 `_id` 使用了自定义的值，值为所定义的数据表的名称，在 `_User` 的数据中，我们能看到定义的 `phone` 字段的定义值以及默认值的设置：

   ```
     "fields_options" : {
       "phone" : {
         "required" : true,
         "defaultValue" : "null"
       }
     }
   ```

    因此在开发中我们除了可以在 `parse-dashboard` 中添加和修改表结构，我们也可以通过其他的 MongoDB 工具进行原始数据的更改。



## 备份和恢复

通常在设计完数据库之后，我会将数据库的 `_SCHEMA` 表完整的备份下来，这样就可以在其他的服务器中初始化我们的数据库结构了。

1. 备份 `_SCHEMA`

   ```
   mongodump -d test -c _SCHEMA -o ./backup
   ```

   MongoDB 的数据库备份通常使用 `mongodump` 命令，表示将数据库指定的数据以 `Bson` 的格式全备份。

   - `-d` 表示备份哪一个数据库
   - `-c` 表示备份数据库中的哪一个表
   - `-o` 表示将文件备份在哪一个目录

   备份完成后，就可以看到 `backup` 目录下添加了 `test` 目录，其中则是备份的 `_SCHEMA` 数据

2. 恢复 `_SCHEMA`

   在我们要初始化的数据库的服务器中，我们需要通过 `mongorestore` 来恢复数据库的 `_SCHEMA` 表结构

   ```
   mongorestore -d test -c _SCHEMA --dir ./backup/test
   ```

   `mongorestore` 的参数和 `mongodump` 基本类似，不同的是 `mongorestore` 的 `--dir` 参数来控制备份恢复的文件目录

   - `-d` 表示备份哪一个数据库
   - `-c` 表示备份数据库中的哪一个表
   - `--dir` 备份恢复的文件目录

只要我们能够把 `_SCHEMA` 的表结构放在项目中，就可以很方便的使用工具来备份和恢复数据了，具体其他的 MongoDB 使用方法和工具可以查看 MongoDB 的[官方文档](https://www.mongodb.com/cn)了解更多哦