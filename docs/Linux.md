





# Linux介绍

# Linux文件管理

# Linux用户管理

# Linux网络管理

# Linux状态监控

# Shell脚本

## Linux基础命令

cut切割命令

date日期命令

grep -v grep过滤本身

wc -l统计

sh -x调试脚本

read

在脚本内部，$$获取运行当前脚本的进程id





## 流程控制

### if

shell中的if和大多数编程语言不一样，示例如下：

```shell
if condition
then
    command1 
    command2
    ...
    commandN
else
    command
fi

-----------------------------------------------------------------------------

if condition1
then
    command1
elif condition2 
then 
    command2
else
    commandN
fi

-------------------------------------------------------------------------------

a=10
b=20
if [ $a == $b ]
then
   echo "a 等于 b"
elif [ $a -gt $b ]
then
   echo "a 大于 b"
elif [ $a -lt $b ]
then
   echo "a 小于 b"
else
   echo "没有符合的条件"
fi
```

### for

```shell
for var in item1 item2 ... itemN
do
    command1
    command2
    ...
    commandN
done

----------------------------------------------------------------

for str in 'This is a string'
do
    echo $str
done
//会将字符串的空格识别为元素分隔符
```

### while

```shell
while condition
do
    command
done

----------------------------------------------------
//无限循环
while :
do
    command
done
```

### case

```shell
echo '输入 1 到 4 之间的数字:'
echo '你输入的数字为:'
read aNum
case $aNum in
    1)  echo '你选择了 1'
    ;;
    2)  echo '你选择了 2'
    ;;
    3)  echo '你选择了 3'
    ;;
    4)  echo '你选择了 4'
    ;;
    *)  echo '你没有输入 1 到 4 之间的数字'
    ;;
esac
```

## 变量

### 变量定义

通过`变量名=值`的方式定义变量，与大多数语言不一样的是，变量名和等号之间不能有空格，其规则基本一致，可下划线开头，不可关键字。

```shell
name=lzx
_age=22
```

### 变量调用

通过`$变量名`的方式使用变量，也可以使用`${变量名}`，优点是容易区分变量范围

```shell
echo $name
echo $_age
```

### 变量替换

变量替换的意思是根据某种匹配规则对变量进行修改，常用的有如下规则：

- ${变量名#匹配规则}：从变量开头进行规则匹配，将符合的最短的数据删除
- ${变量名##匹配规则}：从变量开头进行规则匹配，将符合的最长的数据删除

- ${变量名%匹配规则}：从变量尾部进行规则匹配，将符合的最短的数据删除

- ${变量名%%匹配规则}：从变量尾部进行规则匹配，将符合的最长的数据删除

- ${变量名/旧字符串/新字符串}：用新字符串替换旧字符串，只替换第一个

- ${变量名//旧字符串/新字符串}：用新字符串替换旧字符串，符合条件的全部替换

```shell
msg="This is a msg"
var1=${msg#*is}
echo $var1  -->  结果为is a msg
var2=${msg%msg}
echo $var2  -->  结果为This is a
var3=${msg/is/xx}
echo $var3  -->  结果为Thxx is a msg
var4=${msg//is/xx}
echo $var4  -->  结果为Thxx xx a msg
```

### 字符串变量

- 计算字符串长度
  - ${#变量名}
  - expr length "$变量名"
- 获取子串在字符串中的位置
  - expr index $变量名 $子串
- 计算子串的长度
  - expr match $变量名 子串
- 截取子串
  - ${变量名：index}  从index位置开始截取
  - ${变量名：index：length}  从index位置开始截取，截取长度为length
  - ${变量名：-index}  从右边index位置开始截取
  - expr substr $变量名 $index $length   从index开始截取长度为length子串

```shell
msg="This is a msg"
len=${#msg}
echo len   -->  14
string="quickstart a app"    匹配时是一个一个字符匹配
index=`expr index "$string" sta`
echo index  -->  6
```

> expr命令的索引下标从1开始
>
> ${}索引下标从0开始

### 数组变量

- 数组定义

  -  array=("joe" "mary" "jack")

- 数组访问

  - echo ${array[@]}  全部输出
  - echo ${array[0]}  访问0号元素

- 获取数组长度

  - echo ${#array}   获取数组内元素个数
  - echo ${#array[0]}   获取元素0的长度

- 给某个下标赋值

  - array[0]="lily"

- 删除元素

  - unset arr[0]  删除0号元素
  - unset array   删除数组

- 分片访问

  - ${array[@]:1:4}  访问下标为1到4的元素

- 内容替换

  - ${array[@]/test/TEST}  将数组所有元素包含test的替换为TEST

- 数组遍历

  ```shell
  for v in ${array[@]}
  do
  	echo $v
  done
  ```

### 命令替换

将某个命令的执行结果赋值给变量方式：

- \`command\`
- ${command}

### 数学运算

对变量进行加减乘除等数学运算方式：

- $(($num1 operator $num2))
- expr $num1 operator $num2

```shell
num=$((15 + 10))
num2=$(($num / 5))
```

### 变量类型

shell是一种弱类型语言，使用变量不需要声明类型，但shell变量其实是存在类型的，可以通过以下方式声明：

- declare命令
- typeset命令

这两个命令是完全等价的，常用选项有：

- -r：将变量设置为只读模式
- -i：将变量设置为整数
- -a：将变量设置为数组
- -f：显示所有定义的函数和内容
- -F：显示所有定义的函数，仅显示函数名
- -x：将变量声明为环境变量，即在脚本内外都通用

```shell
typeset -r var1="hello"
echo $var1  -->  hello
var1="hello world"  -->  error,readonly variable
```

> 取消声明为typeset +r，其他选项同理

## 函数

Linux Shell中的函数和大多数编程语言中的函数一样，都是用于封装、复用代码。

- 函数定义

```shell
//第一种
name(){
	.......
}

//第二种
function name
{
	..........
}
```

- 函数调用

```shell
function_name $1 $2.....$n
```

- 函数返回值

```shell
name()
{
	if $1 -eq 100
		return 0
	else
		return 1
	if
}
//使用return只能返回1-255的整数，一般用于判断函数执行状态，0表示成功，1表示失败

name()
{
	if $1 -eq 100
	then
		echo "输入数字等于100"
	else
		echo "输入数字不等于100"
	fi
}
//使用echo可以返回任何字符串，通常用于返回数据，比如一个字符串或者数组
```

- 局部变量

shell中默认变量全部是全局变量，即函数内部定义的变量，函数执行完成后还可以访问。

可以通过local关键字定义局部变量，shell中应慎用全局变量。

- 函数库

shell和其他语言一样，也可以将某些基本功能代码封装成函数库，供其他脚本调用。

```shell
//编辑一个base基础脚本  由于Linux识别文件不通过扩展名 因此扩展名无所谓，但一般为了便于识别，库函数使用lib
vim base.lib
//脚本里调用库函数 必须使用绝对路径
. /root/shell/base.lib
```

## find

find命令用于在磁盘上查找符合条件的文件。

### 语法格式

- find [path] [option] [operator] 

### 常用选项(option)

- -name：根据文件名查找
- -perm：根据文件权限查找
- -prune：排除某些不需要查找的目录
- -user：根据文件属主查找
- -group：根据文件属组查找
- -mtime -n +n：根据文件修改时间查找
- -nogroup：查找无效属组的文件
- -nouser：查找无效属主的文件
- -newer file1 ！ file2 ：查找更改时间比file1新但是比file2旧的文件
- -type：按文件类型查找
- -size -n +n：根据文件大小查找

## grep

grep是过滤器，在指定文件中或者接收其他命令的输出，在其中查找符合条件的内容。

以行为单位查找符合条件的内容，如果该行包含则将该行列出来。

### 语法格式

- grep [option] [pattern] [file1,file2....]
- command | grep [option] [pattern]

### 常用选项(option)

- -v：不显示匹配行信息（即反向匹配）
- -i：搜索时忽略大小写
- -n：显示行号
- -r：递归搜索
- -E：支持扩展正则表达式，等效于egrep
- -F：不支持正则表达式，按字符串意思匹配
- -c：不显示具体信息，只显示匹配到的行数

### Demo

```shell
示例file文件：
[root@instance-cngw2m1f grep]# cat file 
This is a new file!
This file is used to test shell grep!
shell is Linux language!
Shell is Linux language!
SHELL is Linux language!

grep 搜索shell关键字
[root@instance-cngw2m1f grep]# grep shell file
This file is used to test shell grep!
shell is Linux language!

grep -v 反向选择
[root@instance-cngw2m1f grep]# grep -v shell file
This is a new file!
Shell is Linux language!
SHELL is Linux language!

grep -i 忽略大小写
[root@instance-cngw2m1f grep]# grep -i shell file
This file is used to test shell grep!
shell is Linux language!
Shell is Linux language!
SHELL is Linux language!

grep -iv 忽略大小写并反选
[root@instance-cngw2m1f grep]# grep -iv shell file
This is a new file!

grep -n 显示在原文件中的行号
[root@instance-cngw2m1f grep]# grep -n shell file
2:This file is used to test shell grep!
3:shell is Linux language!

grep -E 支持扩展正则表达式
[root@instance-cngw2m1f grep]# grep  "shell|Shell" file
[root@instance-cngw2m1f grep]# grep -E "shell|Shell" file
This file is used to test shell grep!
shell is Linux language!
Shell is Linux language!
```

## sed

Sed（Stream Editor）是流编辑器，对标准输出或者文件逐行进行处理。

### 语法格式

- command | sed [option] '[pattern] [command]'
- sed [option] '[pattern] [command]' file

### 常用选项（option）

- -n：只输出模式匹配行，取消默认设置
- -e：直接在命令行进行sed编辑，不保存文件，默认选项
- -f：匹配模式和命令保存到文件中，指定文件执行
- -r：支持扩展正则表达式
- -i：直接修改文件内容

### 常用匹配模式（Pattern）

- 10command：匹配第10行
- 10,20command：匹配10到20行
- 10，+5command：匹配10到16行
- /pattern1/command：匹配pattern1行
- /pattern1/，/pattern2/command：匹配pattern1行开始，pattern2行结束
- 10，/pattern1/command：匹配10行开始，pattern1行结束
- /pattern1/，10command：匹配pattern1行开始，10行结束

### 常用命令（command）

**查找命令**

- p：将匹配行输出，默认选项

**删除命令**

- d：删除

**修改命令**

- s/old/new：将行内第一个old换成new
- s/old/new/g：将行内所有old换成new
- s/old/new/2：将行内前两个old换成new
- s/old/new/2g：将行内的第两个old开始，后面的全部换成new
- s/old/new/ig：将行内所有old换成new，忽略大小写

**追加内容**

- a：行后追加
- i：行前追加
- r：外部文件读入，行后追加
- w：匹配行写入外部文件

### 反向引用

类似于正则表达式里的反向引用，sed里的反向引用也是指引用之前匹配模式匹配到的内容。可以用&和/两种符号

- & ： 引用之前匹配到的全部内容
- / ： 与正则表达式一样，将前面的匹配模式用()分组，利用/1，/2等去引用部分内容

### 注意事项

> 当匹配模式中包含变量时，应该使用""，单引号会将变量当成常量字符串解析

### Demo

```shell
示例file文件：
[root@instance-cngw2m1f grep]# cat file 
This is a new file!
This file is used to test shell grep!
shell is Linux language!
Shell is Linux language!
SHELL is Linux language!

匹配shell的行进行输出 由于sed会默认将原文件输出 因此匹配shell的行输出两次 
[root@instance-cngw2m1f sed]# sed '/shell/p' file 
This is a new file!
This file is used to test shell grep!
This file is used to test shell grep!
shell is Linux language!
shell is Linux language!
Shell is Linux language!
SHELL is Linux language!

-n 取消静默设置 不在输出原文件
[root@instance-cngw2m1f sed]# sed -n '/shell/p' file 
This file is used to test shell grep!
shell is Linux language!

-e 可以进行多次匹配 只有一个匹配模式时可以省略-e
[root@instance-cngw2m1f sed]# sed -n -e '/shell/p' -e '/Shell/p' file 
This file is used to test shell grep!
shell is Linux language!
Shell is Linux language!

当匹配模式过长时，可以写入文件 -f选项指定文件执行
[root@instance-cngw2m1f sed]# sed -n -f edit.sed file 
This file is used to test shell grep!
shell is Linux language!

将每行的Linux修改为linux并输出
[root@instance-cngw2m1f sed]# sed -n 's/Linux/linux/g;p' file 
This is a new file!
This file is used to test shell grep!
shell is linux language!
Shell is linux language!
SHELL is linux language!

将每行的Linux修改为linux并保存原文件
[root@instance-cngw2m1f sed]# sed -i 's/Linux/linux/g' file 

输出前两行
[root@instance-cngw2m1f sed]# sed -n '1,2p' file 
This is a new file!
This file is used to test shell grep!

匹配This开头的行到S开头的行
[root@instance-cngw2m1f sed]# sed -n '/^This/,/^S/p' file 
This is a new file!
This file is used to test shell grep!
shell is linux language!
Shell is linux language!

删除文件第一行并保存原文件
[root@instance-cngw2m1f sed]# sed -i '1d' file 

删除文件第一行到S开头的行并保存回原文件
[root@instance-cngw2m1f sed]# sed -i '1,/^S/d' file 

删除包含shell的行并输出
[root@instance-cngw2m1f sed]# sed -n '/shell/d;p' file 
This is a new file!
Shell is Linux language!
SHELL is Linux language!

在每个age！结尾的行后追加This is append word!
[root@instance-cngw2m1f sed]# sed -i '/age!$/a This is append word!' file 

在每个age！结尾的行前追加This is two append word!
[root@instance-cngw2m1f sed]# sed -i '/age!$/i This is two append word!' file 

在以new file！结尾的行后追加edit.sed文件里的内容
[root@instance-cngw2m1f sed]# sed -i '/new file!$/r edit.sed' file 

将This开头的行输出到newfile.txt文件中
[root@instance-cngw2m1f sed]# sed -n '/^This/w newfile.txt' file 

将This替换成this并写回原文件
[root@instance-cngw2m1f sed]# sed -i 's/This/this/g' file 

匹配任意一个字符跟着hell的行 在后面追加一个s
[root@instance-cngw2m1f sed]# sed -i 's/.hell/&s/g' file 

删除带注释的行和空行
[root@instance-cngw2m1f sed]# sed -i '/^#/d;/^$/d;/[:blank]*#/d' test 

对非#开头的行前面加上#
[root@instance-cngw2m1f sed]# sed -i 's/^[^#]/&#/g' test 

```

## awk

