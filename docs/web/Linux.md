- [Linux基础介绍](#Linux介绍)
- [Linux文件管理](#Linux文件管理)
- [Linux用户管理](#Linux用户管理)
- [Linux网络管理](#Linux网络管理)
- [Linux软件包管理](#Linux软件包管理)
- [Linux状态监控](#Linux状态监控)
- [Shell基础](#Shell脚本)
- [Shell常用命令](#基础命令)
- [grep](#grep)
- [sed](#sed)
- [awk](#awk)
- [Shell脚本实战](#Shell脚本实战)
- [Linux常见问题](#cjwt)
  - [Linux检测端口连通性的几种方式](#dkltx)
  - [Linux软链接](#rlj)

# <a id="Linux介绍">Linux基础介绍</a>

Linux是一款开源，安全，性能稳定的操作系统，可长期运行，目前大多数应用程序都部署于Linux操作系统之上，是服务端开发的必备知识。

Linux操作系统最初由Linus开发，发布于1991年，后来将其开源，吸引无数开发者加入Linux的开发，目前基于Linux内核有众多的发行版本：

- CentOS（推荐）
- RedHat
- Fedora
- Ubuntu

------

**Linux环境安装**

由于目前大多数人的电脑都是带有GUI界面的Windows系统，因此学习Linux有三种方式：

- 云主机：阿里云、腾讯云等
- PC多系统混跑
- 虚拟机（推荐方式）

虚拟机即在PC上再做一层虚拟化，分出部分计算机资源作为一个新的虚拟PC，在其上可以安装Linux系统用来模拟Linux环境，常见的虚拟机有：

- VMware Workstation
- VirtualBox

虚拟机安装完成后，下载CentOS或者其他Linux发行版的iso镜像即可安装Linux系统，完成Linux环境的搭建。

操作Linux系统的方式有三种：

- 图形终端：类似Windows的GUI操作
- 命令行终端
- SSH远程终端（XShell等）

------

**Linux目录介绍**

Linux系统安装完成后，会自动初始化以下目录：

- /：根目录
- /root：root用户的家目录
- /home/user：普通用户的家目录
- /etc：配置文件目录
- /bin：命令目录
- /sbin：管理命令目录
- /usr/bin /usr/sbin：系统预装的其他命令

------

**Linux常用快捷键**

- Tab：命令和文件名自动补全
- Ctrl + C ：中断正在运行的程序
- Ctrl + D ：结束键盘的输入

------

**Linux万能的帮助命令**

Linux不像Windows的图形化操作界面易于操作，想熟练操作Linux需要记住Linux的操作命令。由于命令众多，Linux内置了帮助文档帮助开发者在需要的时候查看。常见的帮助命令有：

- man

man是manual的缩写，意思是说明书。由于一个命令用于不同场景的作用不同，因此man将某个命令的作用按照场景分为9类，查询时输入数字即可，输入Q退出查询页面。

常见数字如下：

| 序号      | 类型                         |
| --------- | ---------------------------- |
| 1（默认） | 用户在Shell中可操作的指令    |
| 5         | 配置文件                     |
| 8         | 系统管理员可以使用的管理指令 |

```shell
man 1 ls
man 7 man
```

- help 
  - 内部命令使用help command查看帮助
  - 外部命令使用command --help查看帮助

> Shell自带的命令成为内部命令，其他的是外部命令。

```shell
help cd
ls --help
```

- info

info命令的提示信息比help更为详细，可以作为help的补充。

```shell
info ls
```

# <a id="Linux文件管理">Linux文件管理</a>

在Linux中存在一句话"一切皆文件"，在Linux中将任何东西都视为文件，包括文件夹，设备等。即对文件的操作对这些也是通用的。

**文件操作**

- pwd：显示当前文件所在目录

- cd：目录切换
- ls：查看文件信息，后面可跟多个参数查看多个文件信息
  - -l：长格式显示文件
  - -a：显示隐藏文件
  - -r：逆序显示
  - -t：按照时间顺序显示
  - -R：递归显示
- clear：清屏
- mkdir：创建目录
  - -p：递归创建多级目录
- rm：删除文件
  - -r：递归删除
  - -f：不再询问确认
- cp：复制文件
  - -r：递归复制文件夹里的文件
  - -p：除了复制文件外，修改时间和权限一并复制
  - -a：复制目录，保留链接和文件属性，复制文件内容
- mv：剪贴/重命名
- touch：文件创建
- cat：文件查看
- less：小文件查看
- more：大文件查看
- head：文件头查看
  - head -5：查看前五行
- tail：文件尾查看
  - tail -f：实时查看日志
  - tail -10：查看后10行
- wc：统计文本信息
  - wc -l：统计个数

- tar：打包压缩备份命令
  - -c：打包
  - -x：解包
  - -f：制定操作类型为文件
  - -z：通过gzip压缩
  - -v：显示解压过程
  - -C：解压到指定目录

> 早期Linux的备份介质是磁带，使用的命令是tar，可以将打包后的磁带文件进行压缩处理，压缩的命令是gzip和bzip2，因此Linux压缩包的扩展名一般是.tar.gz和.tar.bz2.tgz，实际上Linux的扩展名无意义，只是用于帮助人识别文件类型，Linux的文件识别是依靠ls -l里面的第一个参数识别。

------

**通配符和正则表达式**

Linux支持通配符和正则表达式：

- *：任意个任意字符
- ？：任意一个字符
- []：匹配范围内单个字符
- ^：以什么开头的字符
- $：以什么结尾的字符

------

**Vim编辑器**

Vim是一个多模式文本编辑器，有以下四种模式：

- 正常模式：vim打开文件时即正常模式，可以利用hjkl进行移动，Vim也提供了一些命令来编辑文件中的数据
  - x：删除当前光标所在位置的字符
  - dd：删除光标所在行
  - d$：删除当前光标直到末尾的内容
  - u：撤销上一次命令
  - a：在当前光标后追加数据
  - A：在当前光标所在行尾追加
  - y：复制光标所在字符
  - y$：复制到行尾
  - p：粘贴复制的数据
  - /：查找字符，按n跳下一个
- 插入模式：用于输入字符，正常模式下按"i"进入插入模式
- 命令模式：用于执行保存文件，取消保存等操作，在正常模式下，输入":"进入
  - q：未修改文件退出vim
  - q！：强制退出，不保存
  - w：保存文件
  - wq：保存并退出
  - s/old/new：替换内容，只替换第一次出现的
  - s/old/new/g：替换第一行所有old
  - n,ms/old/new/g：替换n到m行的所有old
  - %s/old/new/g：替换整个文本的old
  - %s/old/new/gc：替换整个文本的old，但每次替换时提示
- 可视模式：在正常模式下，看不出复制了多少数据，因此可以在要复制的位置使用v键进入可视模式，然后复制的内容会高亮显示

# <a id="Linux用户管理">Linux用户管理</a>

Linux系统的权限管理是通过用户和用户组配合使用完成的。

**用户管理**

Linux系统安装完成后，会自带一个root超级管理员用户，可以使用root用户来创建、修改其他用户。

- useradd username：新增用户
  - Linux创建一个用户会做以下几件事
    - 在/home目录下新增用户目录
    - 在/etc/passwd配置文件里新增用户信息
    - 在/etc/shadow配置文件里新增用户信息
    - 新增同名用户组
- id username：查询某个用户信息
- passwd username：更改用户密码
- userdel username：删除用户
- usermod username：修改用户属性
- chage username：修改用户属性
- su：切换用户，root切换其他用户不需要密码
  - su - username：切换用户同时返回用户家目录
- sudo：以其他用户身份执行命令

> Linux会为每个用户设置一个唯一的ID属性，root用户同样有。可以通过usermod修改用户ID，当把root的ID给一个用户时，该用户即拥有root权限。

------

**用户组管理**

每个用户新建时会创建一个同名的用户组，默认属于自己的用户组，一个用户可以属于多个用户组，一个用户组也可以拥有多个用户。

- groupadd groupname：新增用户组
- groupdel groupname：删除用户组

------

**文件类型**

通过ls -l命令可以查看文件的详细信息，第一列即文件的类型。

- \- ：普通文件
- d：目录文件
- b：块特殊文件
- c：字符特殊文件
- l：符号链接文件
- f：命名管道
- s：套接字文件

------

**文件权限**

通过ls -l命令可以查看文件的详细信息，第2-10列即文件的权限信息。每三位一组，2-4代表文件所属用户权限，5-7三位代表用户所在组权限，8-10代表其他用户权限。

不同类型的文件权限意义不一样，以普通文件为例：

- r：可读
- w：可写
- x：可执行

> 如果用数字表示，采用2进制。常用权限666,777.
>
> 通过chmod可以改变文件权限，如chomd 666 test或chmod u+x test
>
> 通过chown可以改变文件的属主，属组

# <a id="Linux网络管理">Linux网络管理</a>

**网络状态查看工具**

CentOS有两套网络管理工具包：

- net-tools：早期的文件管理工具包，包含以下常用命令
  - ifconfig：网卡，ip信息
  - route：路由信息
  - netstat：端口信息
- iproute2：新推出的文件管理工具包，主要对以前的命令进行精简，以参数的形式展示不同内容
  - ip
  - ss

**常用网络故障排除命令**

- ping：检测网络是否连通
- traceroute：检测路由信息
- mtr：结合ping nslookup tracert判断网络连通性
- nslookup：查询DNS域名和IP地址映射
- telnet：检查端口访问
- tcpdump：打印tcp状态
- netstat：端口占用情况
- ss：类似于netstat，获取Socket统计信息

-----

**网络服务管理**

- service network start|restart
- chkconfig -list network
- systemctl start|stop|restart NetworkManger
- systemctl enable|disable NetworkManger

# <a id="Linux软件包管理">Linux软件包管理</a>

**Linux安装软件方式**：

- 软件包管理工具安装
- 源代码编译安装

****

**软件包管理器**

软件包管理器是方便软件安装，卸载，解决软件依赖冲突关系的重要工具

- CentOS，RedHat使用yum包管理器，软件安装格式为rpm
- Debian，Ubuntu使用apt包管理器，软件安装格式为deb

****

**rpm包**

- rpm包格式

vim-comman-7.4.10-5.el7.x86_64.rpm：软件名称-软件版本-系统版本-平台

- rpm命令常用参数
  - -q：查询软件包
  - -qa：查询安装的所有软件包
  - -i：安装软件包
  - -e：卸载软件包

> rpm安装软件包时需要自己解决依赖冲突问题

****

**yum安装软件包**

- yum配置文件
  - /etc/yum.repos.d/CentOS-Base.repo
  - wget -O /etc/yum.repos.d/CentOS-Base.repo覆盖原文件

- yum源
  - http://mirror.centos.org/centos/7/
  - https://opsx.alibaba.com/mirror

- yum常用命令参数
  - install：安装软件包
  - remove：卸载软件包
  - list | grouplist：查看软件包
  - update：更新软件包

****

**源代码编译安装**

- 获取开源的源码压缩包
- 解压
  - tar -xzvf
- 编译
  - make 
- 安装
  - make install

# <a id="Linux状态监控">Linux状态监控</a>

**进程管理**

- Linux中进程的状态
  - D：不可中断
  - R：运行中
  - S：中断
  - T：停止
  - Z：僵死

- 进程查看命令
  - ps
    - ps -ef
    - ps aux
  - ps tree
  - top

- 进程优先级调整
  - nice范围从-20到19，值越小优先级越高
  - renice重新设置优先级
- 进程后台运行
  - &
- 查看后台运行的进程
  - jobs
- 将后台进程掉回前台运行
  - fg 
- 进程间通信方式
  - 管道符
  - 信号
    - ctrl + c
    - kill -l：查看所有信号
    - kill -9：强制退出
- nohup
  - nohup 'command' &：与守护线程类似，后台运行，且输出打印进别的文件，终端关闭也会将继续运行

****

**内存与磁盘管理**

- 内存查看
  - free
  - top
- 磁盘查看
  - fdisk
  - df
  - du

# <a id="Shell脚本">Shell</a>

**Shell介绍**

- Shell是一个命令解释器，用于解释用户对操作系统的操作。

- Shell的种类有很多种，CentOS7默认使用的是bash

```shell
查看Shell种类
[root@instance-cngw2m1f ~]# cat /etc/shells
/bin/sh
/bin/bash
/sbin/nologin
/usr/bin/sh
/usr/bin/bash
/usr/sbin/nologin
/bin/tcsh
/bin/csh
```

------

**Shell脚本**

Shell脚本其实就是一堆Linux命令的集合，为了方便下次使用或者给他人使用，因此写入一个文件里作为Shell脚本。编写一个Shell脚本的一般步骤：

- Sha-Bang
  - 在脚本开头输入"#!bin/bash"即为Sha-Bang
  - 作用是在执行脚本时告诉操作系统采用bash解释器
- 脚本命令
- "#"开头的注释
- chmod u+x添加可执行权限
- 执行命令

****

**Shell脚本执行方式**

Shell脚本可以通过多种方式执行：

- bash ./filename.sh：不需要执行权限
- ./filename.sh：根据Sha-Bang提示选择shell解释器，需要运行权限
- source ./filename.sh：产生新子进程执行
- . filename.sh：产生新子进程执行

> 产生新子进程的可能对当前环境产生影响，如脚本里有cd命令，当脚本运行完之后当前目录也会改变

****

**管道与重定向**

- 管道是将一条命令的输出作为下一条命令的标准输入。

- 重定向是将命令的输出转到别的地方，比如保存在文件里等。
  - <：输入重定向
  - \>：输出重定向，输出到文件里，会清空原文件
  - \>\>：输出重定向，输出到文件里，追加在原文件内容之后
  - 2\>：错误重定向，如果当前操作产生错误，则将错误输出到文件中
  - &\>：全部重定向，无论输出正确还是错误都重定向到文件中

```shell
ps -ef | grep nginx
wc -l | /etc/passwd
read var < a.txt
echo var > b.txt
```

****

## <a id="基础命令">Shell常用命令</a>

- cut：字符切割命令
  - -d：指定分隔符，默认tab
  - -f：获取第几个字段

- date：日期时间命令
  - %Y : 完整年份 (0000-9999)
  - %m : 月份 (01-12)
  - %d : 日 (01-31)
  - %H : 小时(00-23)
  - %M : 分钟(00-59)
  - %S : 秒(00-59)
  - -d：时间计算

- grep：搜索命令
  - -v：过滤命令

> 再用grep搜索时，本身grep命令也会被搜索出来，通过grep -v grep即可过滤掉grep本身命令

- wc：统计文本信息
  - wc -l：统计个数
- sh -x：调试脚本命令，打印脚本运行情况
- read：接收用户输入命令
- $$：在脚本内部，通过$$可以获得执行当前脚本的进程ID，可用于根据id过滤，也可通过$0过滤
- $?：获得上一条命令执行结果
- $#：传入参数个数
- shift：跳过某个参数
- $@：所有剩余参数
- /dev/null：垃圾桶
- exit：程序退出命令
  - exit10：返回10，一般非0表示异常退出

```shell
[root@instance-cngw2m1f ~]# who | cut -f 1 -d " "
root
root

[root@instance-cngw2m1f ~]# date "+%Y-%m-%d %H:%M:%S"
2020-03-19 17:57:03

[root@instance-cngw2m1f ~]# date "+%Y-%m-%d %H:%M:%S" -d "+3 month"
2020-06-19 17:57:18

```

****

**test测试和判断**

- 数值测试
  - -eq：等于
  - -ne：不等于
  - -gt：大于
  - -ge：大于等于
  - -lt：小于
  - -le：小于等于
- 字符串测试
  - =
  - ！=
  - -z ：字符串长度为0
- 文件测试
  - -e：文件存在
  - -r：文件存在且可读
  - -w：文件存在且可写
  - -x：文件存在且可执行
  - -d：目录存在
- 逻辑运算符
  - !：非
  - -a：且
  - -o：或

****

**脚本优先级控制 **

- nice
- renice

避免出现死循环导致CPU占用过高导致死机。

****

**定时任务**

- at：一次性定时任务 ，指 定脚本在什么时候执行
  - atq：查看一次性定时任务
- crontab：周期性定时任务
  - -e：配置周期定时任务
  - -l：查看周期定时任务
  - 配置格式：分钟 小时 日 月 星期

## <a id="流程控制">流程控制</a>

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

## <a id="变量">变量</a>

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

## <a id="函数">函数</a>

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
source /root/shell/base.lib
. test
```

## <a id="find">find</a>

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

## <a id="grep">grep</a>

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

## <a id="sed">sed</a>

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

## <a id="awk">awk</a>

awk是一个数据报告生成器，可以对一个复杂的文本根据我们需要进行处理并生成一个报告。

### 语法格式

- awk 'BEGIN{}pattern{commands}END{}' file_name
- command | awk 'BEGIN{}pattern{commands}END{}'

在文本/标准输出处理之前执行BEGIN里的命令，然后进行根据pattern进行匹配，对匹配到的内容执行commands里的命令，最后执行END里面的命令

### 常用选项

- -v：参数传递，将外部参数传递进awk中
- -f：指定脚本文件执行
- -F：指定分隔符
- -V：查看awk版本号

### 常用匹配模式(Pattern)

- 按正则表达式语法匹配
- 按关系运算匹配
  - \>
  - <
  - \>=
  - <=
  - ==
  - !=
  - ||   或
  - &&   与
  - ！  非
  - ~   匹配正则表达式
  - !~   不匹配正则表达式

### 输出命令(commands)

- print：输出，默认回车换行符
- printf：格式化输出，没有默认回车换行符
  - %s：打印字符串
  - %d：打印十进制数
  - %f：打印一个浮点数
  - %e：以科学计数法打印
  - %c：打印单个字符ASCII码

### awk内置变量

awk中内置了很多默认变量，我们可以通过修改变量更改awk模式：

- $0：整行内容
- $1-$n：当前行的第1-n个字段，根据分隔符计算，分隔符可设置
- NF：当前行的字段个数，即列数
- NR：当前行的行号，多文件处理时连续计数
- FNR：当前行的行号，多文件处理时，每个文件从0开始
- FS：输入字段分隔符，默认以空格或tab分割
- RS：输入行分隔符，默认回车换行
- OFS：输出字段分隔符，默认空格
- ORS：输出行分隔符，默认回车
- FILENAME：当前输入的文件名称
- ARGC：命令行参数个数
- ARGV：命令行参数数组

### awk算数运算符

awk中也可以使用算数运算符对数据进行处理最后再输出。

- \+
- \-
- \*
- /
- %
- ^或** 
- ++x 
- x++

### awk流程控制

- if

```shell
if(条件表达式)
  动作1
else if(条件表达式)
  动作2
else
  动作3 
  
输出第三列大于50且小于100的所有行信息
[root@instance-cngw2m1f awk]#  awk 'BEGIN{FS=":"}{if($3>50 && $3<100) print $0}' /etc/passwd
nobody:x:99:99:Nobody:/:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
postfix:x:89:89::/var/spool/postfix:/sbin/nologin
tcpdump:x:72:72::/:/sbin/nologin

[root@instance-cngw2m1f awk]# awk 'BEGIN{FS=":"}{if($3<50){printf "%-10s%-10s%-5d\n","小于50",$1,$3 } else if($3<100) {printf "%-10s%-10s%-5d\n","小于100",$1,$3}}' /etc/passwd
小于50      root      0    
小于50      bin       1    
小于50      daemon    2    
小于50      adm       3    
小于50      lp        4    
小于50      sync      5    
小于50      shutdown  6    
小于50      halt      7    
小于50      mail      8    
小于50      operator  11   
小于50      games     12   
小于50      ftp       14   
小于100     nobody    99   
小于100     dbus      81   
小于50      rpc       32   
小于100     sshd      74   
小于100     postfix   89   
小于50      ntp       38   
小于100     tcpdump   72   
```

- while

```shell
while(条件表达式)
	动作
	
[root@instance-cngw2m1f awk]# vim while.awk
BEGIN{
	while(i<=100)
	{
		sum+=i
		i++
	}
	print sum	
}

[root@instance-cngw2m1f awk]# awk -f while.awk 
5050
```

- for

```shell
for(初始化;终止条件;计数器更新)
	动作
	
[root@instance-cngw2m1f awk]# vim for.awk
BEGIN{
	for(i=0;i<=100;i++){
		sum+=i
	}
	print sum
}
[root@instance-cngw2m1f awk]# awk -f for.awk 
5050
```

### awk字符串函数

- length(str)：计算字符串长度
- index(str1,str2)：在str1中查找str2位置，返回位置索引
- tolower(str)：转换小写
- toupper(str)：转换大写
- substr(str,m,n)：从str的m位开始截取长度为n的字符串
- split(str,arr,fs)：按fs切割字符串，保存arr数组里，返回切割的个数
- match(str,RE)：在str中按照RE正则表达式查找返回下标
- sub(RE,Repstr,str)：在str中搜索符合RE的字符串，将其替换为Restr，只替换第一个
- gsub(RE,Repstr,str)：在str中搜索符合RE的字符串，将其替换为Restr，全部替换

### Demo

```shell
以：为分隔符输出每行第一个字段
[root@instance-cngw2m1f ~]# awk 'BEGIN{FS=":"}{print $1}' /etc/passwd
root
bin
daemon
........

以：为分隔符输出每一行的字段个数
[root@instance-cngw2m1f ~]# awk 'BEGIN{FS=":"}{print NF}' /etc/passwd
7
7
7
...........

以：为输入分隔符，以|为输出分割符输出每行的第一列和第六列
[root@instance-cngw2m1f ~]# awk 'BEGIN{FS=":";OFS="|"}{print $1,$6}' /etc/passwd
root|/root
bin|/bin
daemon|/sbin
..............

以：为分隔符，将以root开头的行进行输出
[root@instance-cngw2m1f ~]#  awk 'BEGIN{FS=":"}/^root/{print $0}' /etc/passwd
root:x:0:0:root:/root:/bin/bash

以：为分隔符，输出第三列大于50的所有行信息
[root@instance-cngw2m1f ~]# awk 'BEGIN{FS=":"}$3>50{print $0}' /etc/passwd
nobody:x:99:99:Nobody:/:/sbin/nologin
systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin

以：为分隔符，输出第一列为root的所有行信息
[root@instance-cngw2m1f ~]# awk 'BEGIN{FS=":"}$1=="root"{print $0}' /etc/passwd
root:x:0:0:root:/root:/bin/bash

以：为分隔符，输出第三列是三个数字以上的所有行信息
[root@instance-cngw2m1f ~]# awk 'BEGIN{FS=":"}$3~/[0-9]{3,}/{print $0}' /etc/passwd
systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
polkitd:x:999:998:User for polkitd:/:/sbin/nologin

统计空白行个数
[root@instance-cngw2m1f awk]# awk '/^$/{num++}END{print num}' test
13

-v选项引入外部定义变量
[root@instance-cngw2m1f awk]# num=16
[root@instance-cngw2m1f awk]# awk -v num1=$num 'BEGIN{print num1}'
16
```

## <a id="Shell脚本实战">Shell脚本实战</a>

> shell操作MySQL：
>
> mysql -uuser db < test.sql：以user用户的身份在db库中执行sql脚本
>
> mysql命令常用参数：
>
> - -u：指定用户
>
> - -p：用户密码
>
> - -h：IP
>
> - -D：连接的数据库
>
> - -N：不输出列信息
>
> - -B：使用tab键代替默认交互分隔符
>
> - -e：执行SQL
>
> - -E：垂直输出
>
> - -H：以HTML格式输出
>
> - -X：以XML格式输出
>
> grant all on db.table to user@'ip' identify by 'passwd'：将db库的table表的所有操作权限给ip地址的user用户，密码为passwd
>
> MySQL备份命令：mysqldump，常用参数：
>
> - u：用户名
> - p：密码
> - h：IP
> - d：等价于--no-data ，只导出表结构
> - -t：等价于--no-create-info  只导出数据，不导出建表语句
> - -A：等价于--all-databases  导出全部数据库
> - -B：等价于--databases  导出一个或多个数据库

- 利用Shell将文本中某些符合条件的数据导入MySQL

```shell
文本数据：
id	username	passwd
1	lzx			0987
2	mary		8765
3	jack		0965
4	ldt			6579
5	lily		0982
6	rocks		0927
----------------------------------------------
利用Shell将id为奇数且密码以09开头的数据导入MySQL
----------------------------------------------
#!bin/bash
# 从文本导入数据到MySQL

mysql_user="$1"
mysql_passwd="$2"
mysql_host="$3"
db="$4"
table="$5"
file="$6"

mysql_conn="mysql -u"$mysql_user" -p"$mysql_passwd" -h"$mysql_host""

cat $file | while read id username passwd
	do
		new_pd=${passwd:0:2}
		if [ $(($id%2)) -eq 0 -a $new_pd = "09" ]
		then
			$mysql_conn -e "INSERT INTO "$db"."$table" values('$id','$username','$passwd')"
		fi
	done
```

- 利用Shell将MySQL某个表备份，并将备份数据通过FTP传输到远端主机

```shell
FTP常用命令：
open/ ip：与ip地址的主机建立连接
user：登录FTP服务器的用户名和密码
get/mget filename [newname]:下载文件
put/send filename [newname]:上传文件
close/quit:退出ftp
rename filename newfilename:重命名FTP服务器上的文件 
delete filename:删除FTP服务器上的文件 
- 参数
-v 禁止显示远程服务器响应。
-n 禁止自动登录到初始连接。
-i 多个文件传送时关闭交互提示。
------------------------------------------------
#!bin/bash
# 备份MySQL数据通过ftp传输远端

mysql_user="shell"
mysql_passwd="123456"
mysql_host="localhost"
db="test"
table="user"

ftp_ip="106.13.146.213"
ftp_user="ftp_user"
ftp_password="980526"
dst_dir="/data/ftp"
time_date="`date +%Y%m%d%H%M%S`"

file_name="user_info_${time_date}.sql"

function login_ftp
{
ftp -inv << EOF
        open "$ftp_ip"
        user "$ftp_user" "$ftp_password"
        
        cd "$dst_dir"
        put "$1"        
        bye
EOF
}
mysqldump -u"$mysql_user" -p"$mysql_passwd" -h"$mysql_host" test user > $file_name && login_ftp $file_name
------------------------------------------
可以通过crontab定时任务不断执行脚本，实现定时备份
```

- 根据配置文件实现对进程的批量管理

```shell
配置文件格式：
# 所有分组列表
[Group_list]
WEB
DB
Other

# WEB分组
[WEB]
httpd
nginx
tomcat

# 数据库分组
[DB]
mysql
redis
neo4j

# Other分组
[Other]
springboot-kafka
springboot-redis

# http服务
[httpd]

# nginx服务
[nginx]


# tomcat服务
[tomcat]

# mysql服务
[mysql]

# redis服务
[redis]

# springboot-kafka服务
[springboot-kafka]

# springboot-redis服务
[springboot-redis]

----------------------------------------------------
查看进程状态
	- 无参数：全部查看
	- -p参数：指定进程名查看 可以指定多个
	- -g参数：指定分组查看 可指定多个
vim process_status.sh
#！bin/bash
#
# 进程管理程序，根据process.conf配置文件查看进程状态

# 变量
BASE_DIR="/root/shell/process"
CONF_FILE="process.conf"
this_pid=$$

# 获取所有分组
function get_all_group
{
	group_list=`sed -n '/\[Group_list\]/,/\[.*\]/p' $BASE_DIR/$CONF_FILE | grep -v grep | grep -v "#.*" | 
										grep -v "^$" | grep -v "\[.*\]" | grep -v $this_pid`
	echo "$group_list" 
}


# 根据分组获取组内所有进程
#	- 参数一：组名
function get_process_by_group
{
	process=`sed -n '/\[$1\]/,/\[.*\]/p' $BASE_DIR/$CONF_FILE | grep -v grep | grep -v "#.*" | 
										grep -v "^$" | grep -v "\[.*\]" | grep -v $this_pid`
	echo "$process"
}


# 获取所有进程
function get_all_process
{
	for g in `get_all_group`
	do
		process_list=`get_process_by_gourp $g`
		echo "$process_list"
	done
}


# 根据进程名获取进程id
# 	- 参数一：进程名
function get_processid_by_processname
{
	pids=ps -ef | grep $1 | grep -v grep | grep -v $this_pid | grep -v $0 | awk '{print $2}'
	echo "$pids"
}

# 获取进程详细信息
# 	- 参数一：进程id
function get_process_info_by_processid
{
	ps -ef | awk -v p_id=$1 '$2==p_id' &> dev/null
	if [$? -ne 0]
	then
		process_status="RUNNING"
	else
		process_status="STOPPED"
	fi
	process_cpu=`ps aux | awk -v p_id=$1 '$2==p_id{print $3}'`
	process_mem=`ps aux | awk -v p_id=$1 '$2==p_id{print $4}'`
	process_start_time=`ps -p $1 -o lstart | grep -v STARTED`
}


# 进程信息格式化输出
# 	- 参数一：进程名
# 	- 参数二：组名
# 	- 针对每个进程ID输出
function format_process_info
{
        ps -ef | grep $1 | grep -v grep | grep -v $this_pid &> /dev/null
        if [ $? -eq 0 ]
        then
                pids=`get_processid_by_processname $1`
                for pid in $pids
                do
                        get_process_info_by_processid $pid
                        awk -v p_name=$1 \
                                -v g_name=$2 \
                                -v p_id=$pid \
                                -v p_status=$process_status \
                                -v p_cpu=$process_cpu \
                                -v p_mem=$process_mem \
                                -v p_time="$process_start_time" \
                                'BEGIN{printf "%-20s%-10s%-10s%-10s%-10s%-10s%-20s\n",p_name,g_name,p_id,p_status,p_cpu,p_mem,p_time}'
                done
        else
                awk -v p_name=$1 \
                        -v g_name=$2 \
                        'BEGIN{printf "%-20s%-10s%-10s%-10s%-10s%-10s%-20s\n",p_name,g_name,"NULL","NULL","NULL","NULL","NULL"}'
        fi
}



# 判断组名是否在配置文件中
#	- 参数一为组名
#	- 返回0代表在配置文件中 1不在
function is_group_in_conf
{
	for gn in `get_all_group`
	do
		if [ $gn == $1 ]
		then
			return 0
		fi
	done
	return 1
}

# 判断进程名是否在配置文件中
#	- 参数一为进程名
#	- 返回0代表在配置文件中 1不在
function is_process_in_conf
{
	for pn in `get_all_process`
	do
		if [ $pn == $1 ]
		then
			return 0
		fi
	done
	return 1
}

# 根据进程名称获取组名称
# 	- 参数一：进程名
# 	- 返回值：组名
function get_group_by_process
{
	for gn in `get_all_group`
	do
		for pn in `get_process_by_group $gn`
		do
			if [ $pn == $1 ]
			then
				echo $gn
			fi
	done
}

# 配置文件检查
function check_conf
{
	if [ ! -e $BASE_DIR/CONF_FILE ]
	then
		echo "$BASE_DIR/CONF_FILE is not exist..."
		exit 1
	fi
}



	


check_conf
awk 'BEGIN{printf "%-20s%-15s%-10s%-10s%-10s%-10s%-20s\n","ProcessName","GroupName","PID","Status","CPU","Memory","StartTime"}'
# 获取输入参数
if [ $# -eq 0 ]
then
        for pn in `get_all_process`
        do
                echo $pn
        done
elif [ $# -ge 2 -a $1 == "-g" ]
then
        shift
        for gn in $@
        do
        	is_group_in_conf $gn
        	if [ $? -eq 1 ]
        	then
        		echo "$gn is not exist in process.conf"
        	else
            	for pn in `get_process_by_group $gn`
            	do
            	        format_process_info $pn $gn
            	done
            fi
        done
elif [ $# -ge 2 -a $1 == "-p" ]
then
        shift
        for pn in $@
        do
                is_process_in_conf $pn
                if [ $? -eq 1 ]
                then
                	echo "$pn is not exist in process.conf"
                else
                	gn=`get_group_by_process $pn`
                	format_process_info $pn $gn
                fi           
        done
else
        echo "Param Exception...Please check.."
fi
```

# <a id="cjwt">Linux常见问题</a>

### <a id="dkltx">Linux检测端口连通性的几种方式</a>

- telnet

telnet为用户提供了在本地计算机上完成远程主机工作的能力,因此可以通过telnet来测试端口的连通性,具体用法格式: `telnet ip port`

**ip:**是测试主机的ip地址

**port:**是端口,比如80

> 如果远程主机没开启该端口，会一直提示尝试连接

- ssh

SSH 是目前较可靠,专为远程登录会话和其他网络服务提供安全性的协议,在linux上可以通过ssh命令来测试端口的连通性,具体用法格式如下:`ssh -v -p port username@ip `

**-v** 调试模式(会打印日志).

**-p** 指定端口

**username:**远程主机的登录用户（可省略）

**ip:**远程主机

> 如果远程主机开通了相应的端口,会提示Connected established，如果没有开启该端口，会一直有debug日志，尝试连接

- curl

curl是利用URL语法在命令行方式下工作的开源文件传输工具。也可以用来测试端口的连通性,具体用法:`curl ip:port`

**ip**:是测试主机的ip地址

**port:**是端口,比如80

> 如果远程主机开通了相应的端口,都会输出信息,如果没有开通相应的端口,则没有任何提示,需要CTRL+C断开。

- wget

wget是一个从网络上自动下载文件的自由工具**,支持通过HTTP、HTTPS、FTP三个最常见的TCP/IP协议下载**,并可以使用HTTP代理。wget名称的由来是**“World Wide Web”与“get”的结**合,它也可以用来测试端口的连通性具体用法:`wget ip:port`

**ip:**是测试主机的ip地址

**port:**是端口,比如80

> 如果远程主机不存在端口则会一直提示连接主机。

### <a id="rlj">Linux软链接</a>

> 软链接本质上是一种引用操作，Linux中的软链接和Windows中的快捷方式差不多。

- 创建软链接：ln -s [源文件/目录] [目标文件或目录]

```shell
# 将/opt/rocks/demo.war的引用指向/opt/rocks/repository/services/demo.war
ln -s /opt/rocks/repository/services/demo.war /opt/rocks/demo.war
```

- 删除软链接：rm -rf 软链接名字

```shell
# 删除软链接
rm -rf /opt/rocks/demo.war
```

- 修改软链接：ln -snf [新的源文件/目录] [目标文件或目录]

```shell
# 修改软链接
ln -snf /opt/rocks/repository/services/demo2.war /opt/rocks/demo.war
```

- 软链接与硬链接

软链接：和Windows的快捷方式作用一样。

硬链接：等同于执行一个`cp -p`命令，将源文件复制一份并将文件的元信息也一并修改，同时保持后续更新，当源文件更新时，目标文件也会一起更新。

- ln相关参数

常用的参数：

--help：帮助文档

-b 删除，覆盖以前建立的链接

-d 允许超级用户制作目录的硬链接

-f 强制执行

-i 交互模式，文件存在则提示用户是否覆盖

-n 把符号链接视为一般目录

-s 软链接(符号链接)

-v 显示详细的处理过程