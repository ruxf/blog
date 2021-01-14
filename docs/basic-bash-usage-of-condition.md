在服务器和后端开发中，经常不可避免的需要使用各种各样的命令来完成一些操作，而这些命令大多时候都是重复的，重复的操作带来的只是浪费时间，也可以说是失去了摸鱼的时间呢，所以我们可以学习编写基本的 Bash 脚本来减少重复的工作，比如国外的大神开源的各种 Hacker 脚本，想了解的同学可以看这里，[传送门](https://github.com/NARKOZ/hacker-scripts)

而对于脚本小白来说，今天我们一起来学习一下基本的 Bash 脚本语法以及 `Bash` 中的逻辑判断：

- `if`

- `if-else`
- `if-elif-else`

## 文件

### 第一行

脚本开始的第一行是 `#!/usr/bin/bash`，这段代表表示的是脚本的执行程序是 `/usr/bin/bash`，如果是 `Python` 脚本那就是 `#!/usr/bin/env python`。

第一行的内容叫做 `Shebang`（也称为 `Hashbang`） 语法，是一个由井号和叹号构成的字符序列 `#!`，其出现在文本文件的第一行的前两个字符。 在文件中存在 `Shebang `的情况下，类 `Unix` 操作系统的程序加载器会分析`Shebang` 后的内容，将这些内容作为解释器指令，并调用该指令，并将载有 `Shebang` 的文件路径作为该解释器的参数。

### 注释

`Bash` 的语法中注释是使用的 `#` 后面添加的是注释的内容（除了第一行的 `Shebang` ）

```bash
#!/usr/bin/bash
# 注释的内容

```

### 变量

在 Bash 中使用 `${}` 来使用变量，而参数代表的 `1` `2` `@` 也是变量，所以 `$1` 等同于 `${1}`

`$1` `$2` `$3` 获取的是执行命令后的第 n 个参数

示例：新建一个 `param.sh`，添加执行权限

```bash
touch param.sh
chmod +x param.sh
```

添加内容

```bash
#!/usr/bin/bash

echo ${@}

echo ${0}
echo ${1}
echo ${2}
```

在命令行中测试文件

```bash
./param.sh first second
```

输出

```bash
first second
./param.sh
first
second
```

输出结果可以看到 `${@}` 输出除了脚本名称之外的其他参数，`${0}` 输出脚本的名称， `${1}` 输出第一个参数，`${2}` 输出第二个参数，以此类推，在 [GitHub](https://github.com/hom/basic-bash-script/blob/master/param.sh) 中查看源代码。

## 逻辑判断

### if

在 Bash 脚本中，`if` 的使用是比较常用的，它和常用的脚本语言中的 `if` 逻辑判断不同的在于 `Bash` 中判断的是命令执行的结果是否成功（返回 `0`代表执行成功）

参数格式：

```bash
if [CONDITION]; then
	[COMMAND]
fi
```

示例 1：

判断用户是否输入名字

```bash
#!/bin/bash

read -p "What is your name? " name

if [[ -z ${name} ]]
then
  echo "Please enter your name!"
else
  echo "Hello, welcome ${name}"
fi
```

脚本中的 `-z` 代表的是判断变量是否存在，所以如果输入了名字则打印出用户名，如果未输入则提醒用户输入用户名

**未输入用户名**

```bash
./if.sh

# Output
What is your name? 
Please enter your name!
```

**输入用户名**

```bash
./if.sh

# Output
What is your name? 孟太白
Hello, welcome 孟太白
```

在 GitHub 中查看源代码，[传送门](https://github.com/hom/basic-bash-script/blob/master/if.sh)。

### if-else

如果在逻辑判断中有两个分支来判断，则需要我们的 `if-else` 逻辑块。

参数格式

```bash
if [CONDITION]; then
	[COMMAND]
else
	[COMMAND]
fi
```

示例：

判断是否输入用户名并且是否是指定的用户名称

```bash
#!/bin/bash

admin=mengj

read -p "What is your name? " name

if [[ -n ${name} ]] && [[ ${name} == ${admin} ]]; then
  echo "Hello, welcome ${name}, you are the admin user"
else
  echo "Please enter your name!"
fi

```

可以在 GitHub 中查看源代码，[传送门](https://github.com/hom/basic-bash-script/blob/master/if-else.sh)。

### if-elif-else

和 `Python` 等编程语言一样，`Bash` 也有 `if-elif-else` 的多重逻辑判断，需要注意的是 `elif` 的判断之后也要加上 `; then`

参数格式：

```bash
if [CONDITION]; then
	[COMMAND]
elif [CONDITION]; then
	[COMMAND]
else
	[COMMAND]
fi
```



示例：

**判断输入的用户名是否是指定的用户**

```bash
#!/bin/bash

admin=mengj

read -p "What is your name? " name

if [[ -z ${name} ]]; then
  echo "Please enter your name!"
elif [[ ${name} == ${admin} ]]; then
  echo "Hello, welcome ${name}, you are the admin user"
else
  echo "Hello, welcome ${name}"
fi

```

脚本测试的内容和 `if` 的输出结果基本类似，不同的是多了一重判断，判断输入的用户名是否和 `mengj` 一致，可以在 `GitHub` 中查看源代码，[传送门](https://github.com/hom/basic-bash-script/blob/master/if-elif-else.sh)。

### 判断参数

在逻辑判断中，比较常用的判断就是文件和字符串之间的判断，比如前面示例中的 `-z` 参数，下面的内容就是 `Bash` 中常用的判断。

#### 文件

**单个文件的判断**

- `[ -a FILE ]` 判断文件是否存在
- `[ -b FILE ]` 判断文件是否存在并且是一个块文件
- `[ -c FILE ]` 判断文件是否存在并且是一个字符文件
- `[ -d FILE ]` 判断文件是否存在并且是一个目录
- `[ -e FILE ]` 判断文件是否存在
- `[ -f FILE ]` 判断文件是否存在并且是一个常规文件
- `[ -g FILE ]` 判断文件是否存在并且它的 `SGID` 已设置
- `[ -h FILE ]` 判断文件是否存在并且是一个系统链接
- `[ -k FILE ]` 判断文件是否存在并且设置了 `sticky bit`
- `[ -p FILE ]` 判断文件是否存在并且是一个管道文件 `FIFO`
- `[ -r FILE ]` 判断文件是否存在并且可读
- `[ -s FILE ]` 判断文件是否存在并且内容不为空
- `[ -t FD ]` 判断文件描述符是否可读并指向一个终端
- `[ -u FILE ]` 判断文件是否存在并且它的 `SUID` 已设置
- `[ -w FILE ]` 判断文件是否存在并且可写
- `[ -x FILE ]` 判断文件是否存在并且可执行
- `[ -O FILE ]` 判断文件是否存在并且属于有效的用户 ID
- `[ -G FILE]` 判断文件是否存在并且属于有效的组 ID
- `[ -L FILE ]` 判断文件是否存在并且是一个符号链接
- `[ -N FILE ]` 判断文件是否存在并且从上次读取之后未被修改
- `[ -S FILE ]` 判断文件是否存在并且是一个 `SOCKET`



**两个文件的判断**

- `[ FILE1 -nt FILE2]` 如果 `FILE1` 比 `FILE2` 的更新时间最近，或者 `FILE1` 存在而 `FILE2` 不存在，则为`true`
- `[ FILE -ot FILE2 ]` 如果 `FILE1` 比 `FILE2` 的更新时间更旧，或者 `FILE2` 存在而 `FILE1` 不存在，则为`true`
- `[ FILE1 -ef FILE2]` 如果 `FILE1` 和 `FILE2` 引用相同的设备和 `inode` 编号，则为`true`

#### 字符串

- `[ string ]`：如果`string`不为空（长度大于0），则判断为真。
- `[ -n string ]`：如果字符串`string`的长度大于零，则判断为真。
- `[ -z string ]`：如果字符串`string`的长度为零，则判断为真。
- `[ string1 = string2 ]`：如果`string1`和`string2`相同，则判断为真。
- `[ string1 == string2 ]` 等同于`[ string1 = string2 ]`。
- `[ string1 != string2 ]`：如果`string1`和`string2`不相同，则判断为真。
- `[ string1 '>' string2 ]`：如果按照字典顺序`string1`排列在`string2`之后，则判断为真。
- `[ string1 '<' string2 ]`：如果按照字典顺序`string1`排列在`string2`之前，则判断为真。

**整数判断**

- `[ integer1 -eq integer2 ]`：如果`integer1`等于`integer2`，则为`true`。
- `[ integer1 -ne integer2 ]`：如果`integer1`不等于`integer2`，则为`true`。
- `[ integer1 -le integer2 ]`：如果`integer1`小于或等于`integer2`，则为`true`。
- `[ integer1 -lt integer2 ]`：如果`integer1`小于`integer2`，则为`true`。
- `[ integer1 -ge integer2 ]`：如果`integer1`大于或等于`integer2`，则为`true`。
- `[ integer1 -gt integer2 ]`：如果`integer1`大于`integer2`，则为`true`