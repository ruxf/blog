在 `Bash` 脚本中，我们经常需要引用变量或者命令的执行结果来作为文件的名字或者日志的输出，在脚本或者 `Shell` 中我们可以使用反引号 ` 或者括号 () 来获取到变量的内容

## 示例
示例 1
获取日期的输出
```bash
echo `date`
# output
2021年 01月 05日 星期二 15:21:23 CST
```
```bash
echo $(date)
# output
2021年 01月 05日 星期二 15:21:32 CST
```
通过使用反引号或者括号我们可以将命令的输出做为脚本中的内容输入，同样可以使用命令的参数来格式化输出

示例 2
获取 `年-月-日` 格式的日期

```bash
echo `date +%F`
# output
2021-01-05
```

```bash
echo $(date +%F)
# output
2021-01-05
```

## 讨论
在 `Bash` 中反引号和括号代表命令输入，所以在脚本中我们只能使用反引号和括号来获取命令的执行和输出结果，而不能使用脚本中声明的变量。而使用 `$` 和 `${}`  可以获取脚本中声明的变量而不能获取命令的输入
```bash
#!/usr/bin/bash

variable=name

# use `` to output variable
echo `variable`

# use $ to output variable
echo $variable

# use $() to output variable
echo $(variable)

# use ${} to output variable
echo ${variable}
```

执行的结果报错
```bash
▶ ./test
./test:行6: variable: 未找到命令

name
./test:行12: variable: 未找到命令

name
```

## 更多内容

更多的内容请关注[GitHub](https://github.com/hom)，知乎@[大手印](https://www.zhihu.com/people/mrmengj)，或者关注我的公众号@全栈开发师，我会不定时分享一些全栈方向的开发内容，谢谢分享

![](https://tva1.sinaimg.cn/large/007S8ZIlly1ggms66rig8j31bi0hcgol.jpg)