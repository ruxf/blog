在上一篇文章中添加了 `Bash` 脚本中的基本语法已经逻辑判断 `if`  `if-else` `if-elif-else`，在本章节中你将学习到 `Bash` 中的循环逻辑，包括：

- `for`
- `while`
- `until`
- 使用 `continue` `break` 控制循环

## 循环

在常用的逻辑交互中，自然不能少了循环的逻辑，巧妙的使用循环，能够节省不少的开发时间呢

### for

`for` 允许类每一次循环的时候执行判断命令，如果执行结果通过则执行该次循环。

格式

```bash
for NAME [ in LIST ]; do
  [COMMAND]
done
```

示例 1：

```bash
#!/bin/bash

# Echo list with space deviced
users='mengj guom tj'
for user in ${users}; do
  echo ${user}
done
```

使用 `for` 循环默认使用 ` ` 作为 `LIST` 的分隔符，上面的示例中可以在终端中输出列表中的每一项。

```bash
mengj
guom
tj
```

示例 2：

默认的分隔符是 ` `，因此我们可以通过更改列表的分隔符，更改分隔符通过声明 `IFS`（Internal FIeld Seprator） 来打印不同的 `LIST` 子元素。

```bash
list='mengj,guom,tj'
IFS=,
for item in ${list}; do
  echo ${item}
done
```

上面的示例中我们声明了分隔符 `IFS` 是 `,`，因此输出的结果和示例 1 的结果相同

```bash
mengj
guom
tj
```

示例 3：

同时我们也可以通过声明列表的范围，来实现控制 `for` 循环的遍历次数，声明返回需要使用 `{}` ，花括号内部是符合 `Unix` 的参数范围约定， 即 `{1..10}` 代表的是 1 - 10。

```bash
# Echo range numbers
for num in {1..10}; do
  echo ${num}
done
```

示例 3 输出的结果是从 1 开始一直输出到 10 为止，需要注意的是在 `Bash` 的使用 `echo` 默认在结果后添加 `\n` 回车符

```bash
1
2
3
4
5
6
7
8
9
10
```

示例 4：

同时 `for` 循环的列表也可以是多个字符串以 `()` 包裹的数组形式。

```bash
# Echo array
array=('mengj' 'guom' 'tj')
for item in ${array[@]}; do
  echo ${item}
done

for item in ${array[*]}; do
  echo ${item}
done
```

在示例 4 中，我们看到使用了 `${array[@]}` 和 `${array[*]}`，这两种形式都是代表的数组本身哦

示例的内容可以从 GitHub 中查看源代码，[传送门](https://github.com/hom/basic-bash-script/blob/master/for.sh)。

### while

`while` 的代码格式和 `if` 相似，不同的是 `while` 能够重复的执行逻辑判断。

**格式**

```bash
while [CONDITION]
do
  [COMMANDS]
done
```

示例：

执行指定的循环10次。

```bash
#!/bin/bash

counter=1
while [[ ${counter} -lt 10 ]]; do
  echo ${counter}
  ((counter++))
done

```

在 GitHub 中查看源代码，[传送门](https://github.com/hom/basic-bash-script/blob/master/while.sh)。

### until

`until` 的循环判断和 `while` 相比更加的简单，`until` 在执行命令的前提下需要执行测试命令。

示例：

```bash
#!/bin/bash

counter=1
until [[ ${counter} -gt 10 ]]; do
  echo ${counter}
  ((counter++))
done

```

我们可以比较一下 `until` 和 `while` 循环的不同之处，`while` 循环使用的是判断条件，而 `until` 使用的是测试命令，从输出结果中可以看出两个命令的执行结果是相同的。

```bash
1
2
3
4
5
6
7
8
9
10
```

`until` 的示例源代码已经放在了 `GitHub` 中，[传送门](https://github.com/hom/basic-bash-script/blob/master/until.sh)。

### 循环控制

循环控制主要讲述 `continue` 和 `break`，从字面意思中我们可以看到 `continue` 是继续的意思，其实 `continue` 命令是跳过本次循环，然后开始下次循环；而 `break` 字面上意思是打断的意思，因此 `break` 的命令是中断循环，执行循环后面的内容。

示例 1：

```bash


#!/bin/bash

# 通过使用 while 循环和 case 配合 break 进行循环控制

while true; do
echo "On which topic do you want advice?"
echo "1.  politics"
echo "2.  startrek"
echo "3.  kernelnewbies"
echo "4.  sports"
echo "5.  bofh-excuses"
echo "6.  magic"
echo "7.  love"
echo "8.  literature"
echo "9.  drugs"
echo "10. education"
echo

echo -n "Enter your choice, or 0 for exit: "
read choice
echo

case $choice in
     1)
      echo 'politics'
     ;;
     2)
      echo 'startrek'
     ;;
     3)
      echo 'kernelnewbies'
     ;;
     4)
     echo 'sports'
     ;;
     5)
      echo 'bofh-excuses'
     ;;
     6)
      echo 'magic'
     ;;
     7)
      echo 'love'
     ;;
     8)
      echo 'literature'
     ;;
     9)
      echo 'drugs'
     ;;
     10)
      echo 'education'
     ;;
     0)
     echo "OK, see you!"
     break
     ;;
     *)
     echo "请从 0 - 10 中选择你喜欢的某一项"
     ;;
esac  
done

```

该示例中使用 `while` 和 `case` 来打印用户选择的结果，如果输入 `0` 则通过使用 `break` 退出循环，查看源代码的[传送门](https://github.com/hom/basic-bash-script/blob/master/break.sh)。

示例 2：

使用 `continue` 命令来输出 0 - 10, 如果能被 3 整除则跳过此次循环，只输出不能被 3 整除的数字。

> Talk is easy, show my your code

```bash
#!/bin/bash

for i in {1..10}; do
  if [ $(( ${i} % 3 )) -eq 0 ]; then
    continue
  fi
  
  echo ${i}
done

```

示例的代码中通过使用 `for` 来打印 1-10 的数字，如果是 3 的倍数则使用 `continue` 跳过输出，输出的结果如下：

```bash
1
2
4
5
7
8
10
```

输出的结果中没有 `3` `6` `9` 这 3 个数字，可以在我的 GitHub 中查看并下载源代码执行测试哦，[传送门](https://github.com/hom/basic-bash-script/blob/master/continue.sh)。

