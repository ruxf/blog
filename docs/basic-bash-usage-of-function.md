在 `Bash` 的基本使用中，和其他脚本语言类似，`Bash` 中可以通过使用函数把逻辑块内容封装起来，在本节中，你可以学习到如何使用 `Bash` 的函数，以及与函数相关的几个示例。

- 什么是函数
- 脚本中的函数
- 解析函数的参数
- 什么时候需要使用函数

## 简介

`Bash` 中的函数是可以重复使用的代码片段，有利于代码的复用。它与别名（`alias`）的区别是，别名只适合封装简单的单个命令，函数则可以封装复杂的多行命令，函数总是在当前 `Shell` 执行，这是跟脚本的一个重大区别，`Bash` 会新建一个子 `Shell` 执行脚本。如果函数与脚本同名，函数会优先执行。但是，函数的优先级不如别名，即如果函数与别名同名，那么别名优先执行。

## 语法

在 `Bash` 中声明一个函数和使用 `JavaScript` 中声明函数比较类似（前端开发的同学更容易上手哦），唯一与 `JavaScript` 不同的地方在于函数的调用以及传参的形式。

### 函数声明

函数的声明格式如下：

```bash
function FUNCTION() {
	[COMMAND]
}
```

或者省略掉 `function` 的关键字也是可以的：

```bash
FUNCTION() {
	[COMMAND]
}
```

上面代码中，`FUNCTION` 是自定义的函数名，函数代码就写在大括号之中。这两种写法是等价的。

需要注意的是函数中的命令语句如果是在同一行中则需要以 `;` 分隔，比如：

`COMMAND1; COMMAND2`

### 参数

在函数体内可以使用参数变量，需要注意的是函数体内声明的变量在整个脚本中可以，也就是说会有 `local` 的变量声明，通过对变量声明添加 `local`，可以将变量的作用范围限定在函数体内。

下面是函数中内置的获取函数参数的几个变量

- ``$n` 获取函数的第 n 个参数
- `$0` 获取函数的名称，和在脚本中获取脚本的名称效果一样
- `$#`  获取函数的参数总个数
- `$@` 获取函数的全部参数，中间使用空格分隔
- `$*` 获取函数的全部参数，中间使用变量 `IFS` 来控制，默认使用空格，也可以使用自定义的符号。

### 添加返回值

在函数中我们也可以自定义返回值，通过使用 `return` 可以从函数中返回值，然后在调用脚本的地方通过使用 `$?` 获取到函数返回的值。

## 示例

示例 1：

简单使用 `function` 输出

```bash
#!/bin/bash

function hello() {
  echo "Hello world from function of bash"
}

hello
```

执行该测试文件，我们可以在命令行中看到输出结果

```bash
Hello world from function of bash
```

由上面的示例中我们可以看到，在 `Bash` 中调用函数，不需要添加括号即可调用函数，和在脚本中使用命令是一样的操作方式。

示例 2：

使用 `Bash` 的函数，并解析函数传入的参数

```bash
#!/bin/bash
user=mengj
function hello() {
  echo "Hello ${1} from the function of bash"
}

hello ${user}

```

在上面的示例中我们看到，在 `Bash` 中对函数进行传参，直接在函数名称后面添加参数即可，与在命令行中调用命令的传参方式是一样的，很神奇吧。

脚本的输出中按照我们的预期打印出了 `user` 的变量内容

```bash
Hello mengj from the function of bash
```

使用函数的参数时，我们需要注意，在函数中有自己的作用域，获取参数的方式和在脚本中使用 `${n}` 获取参数是一样的，获取的到的是传进来的参数，而不是顶级作用域执行脚本时传进来的参数。

示例 3： 

获取函数返回的值

```bash
#!/bin/bash

function execute() {
	echo 'Run command in bash function'
	return 10
}

execute

echo "Get execute function returned value is: ${?}"
```

给脚本添加执行权限之后执行脚本，我们可以得到脚本的输出符合预期。

```bash
Run command in bash function
Get execute function returned value is: 10
```

