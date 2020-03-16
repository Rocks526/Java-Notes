# Linux介绍

# Linux文件管理

# Linux用户管理

# Linux网络管理

# Linux状态监控

# Shell脚本

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

