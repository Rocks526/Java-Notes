

- [Python介绍](#js)
- [Python中的数据类型](#sjlx)
- [Python中的运算符](#ysf)
- [Python中的逻辑控制](#kz)
- [Python中的数据结构](#sjjg)
- [Python函数](#hs)
- [Python面向对象](#dx)
- [Python文件操作](#wj)
- [Python异常处理](#yc)
- [Python网络编程](#wl)
- [Python多线程](#xc)
- [Python图形化界面](#txh)
- [Python常用模块](#mk)
- [Python操作数据库](#sjk)

- [Python操作Excel](#excel)

# <a id="js">Python介绍</a>

### Python历史

Python诞生于1989年圣诞节，作者是吉多·范罗苏姆（Guido van Rossum），设计目标成为是一个简单直观，高效，开源的**解释性**语言。1991 年，第一个 Python解释器诞生，它是用 C 语言实现的，并能够调用 C 语言的库文件。

### Python特点

- 开发简单，代码量少
- 解释性语言
- 弱类型语言
- 面向对象
- 免费开源
- 强大的标准库
- 大量第三方模块，涵盖科学计算，人工智能，机器学习，Web开发等多个领域

### Python常用开发环境

- PyCharm
- VSC
- Sublime

### Python基础概念

- Python标识符，关键字和Java等其他语言基本保持一致
- Python变量声明时不需要指定数据类型，与Kotlin不同，Kotlin虽然也不需要指定数据类型，但编译器会自动推断数据类型，而且指定之后不可修改数据类型，而Python的数据类型可以随意修改，与Shell类似
- 严格意义来说，Python没有常量，只能变量当常量使用，代码安全需要程序员自己检查
- Python注释

```python
# 单行注释 ： #开头加一个空格
#

# 多行注释 
"""
  这是一段注释
"""
```

- Python中一个文件就是一个模块，模块内可以声明变量，常量，函数，属性和类等元素，一个模块可以提供对另一个模块的访问
  - 引入另一个模块时，会将该模块执行一遍
  - 通过import关键字引入，可以全部引入，也可以引入部分元素
  - 可以通过as起别名

```python
module1:
y = True
z = 5.26

print("进入module1模块！")

module2:
import module1
from module1 import z

y = 20

# 访问当前模块 y
print(y)
# 访问module的y
print(module1.y)
# 访问module1的z
print(z)

执行结果：
进入module1模块！
20
True
5.26
```

- Python中的包和Java类似，是一种命名空间，用于解决名称冲突问题，引入模块时前面加上包名即可

### Python中的命名规范

- 包名：全小写，用"."分隔
- 模块名：全小写，用"_"分隔
- 类名，异常名：驼峰命名法
- 变量名，函数名，方法名：全小写，用"_"分隔
- 常量名：全大写，用"_"分隔

# <a id="sjlx">Python中的数据类型</a>

在Python中所有的数据类型都是类，每一个变量都是类的实例，没有基本数据类型的概念。

Python提供了6种标准数据类型：数字，字符串，列表，元组，集合和字典，其中后四项为封装的数据结构。

### 数字类型

Python 数字类型有 4 种： 整数类型、浮点类型、复数类型和布尔类型。需要注意的是，布尔类型也是数字类型，它事实上是整数类型的一种。 

#### 整数

与Java不同，Python不再区分short，int，long，统称为整型，用int表示，有二进制，八进制，十进制和十六进制。

#### 浮点数

Python中的浮点数只有float

#### 复数类型

复数在数学中是非常重要的概念，无论是在理论物理学，还是在电气工程实践中都经常使用。但是很多计算机语言都不支持复数，而 Python 是支持复数的，这使得 Python 能够很好地用来进行科学计算。 

Python 中复数类型为 complex，例如1+2j 表示的是实部为 1、虚部为 2 的复数。

#### 布尔类型

Python 中布尔类型为 bool， bool 是 int 的子类，它只有两个值： True 和 False 

任何类型数据都可以通过 bool()函数转换为布尔值，那些被认为“没有的”“空的” 值会转换为 False，反之转换为 True。 如None（空对象）、False、 0、0.0、0j(复数)、""(空字符串)、 \[\](空列表)、()(空元组)和{}(空字典)这些数值会转换为 False，否则是 True

#### 数字之间的转换

- 数字类型之间进行计算时会进行隐式转换

- 可以通过type()函数查看类型
- 也可以通过bool()，float()，int()函数进行显式转换

### 字符串类型

由字符组成的一串字符序列称为“字符串 ”， 字符串是有顺序的，从左到右，索引从 0 开始依次递增。 Python 中字符串类型是 str

#### 字符串表示方式

- 普通字符串：'demo'或"demo"
- 原始字符串：在普通字符串的前面加r，表示字符串里的特殊字符不需要转义
- 长字符串：包含换行缩进等固定格式的字符串，通过'''或者"""表示

```python
# 字符串表示

# 普通字符串
str1 = 'demo\tdemo'
print(str1)
str2 = "demo\tdemo"
print(str2)

# 原始字符串
str3 = r"demo\tdemo"
print(str3)

# 长字符串
str4 = """
    demo\tdemo
"""
print(str4)

结果：
demo	demo
demo	demo
demo\tdemo

    demo	demo

```

#### 字符串格式化输出

```python
# 字符串格式化输出
name = "lzx"
age = 22
res = "他的名字是{0}，年龄是{1}".format(name,age)
print(res)
res2 = "他的名字是{n}，年龄是{a}".format(n=name,a=age)
print(res2)

他的名字是lzx，年龄是22
他的名字是lzx，年龄是22

# 字符串格式化输出
name = "lzx"
age = 22
money = 2559.6
res = "他的名字是{0:s}，年龄是{1:d}，剩余金钱是{2:.3f}".format(name,age,money)
print(res)
res2 = "他的名字是{n:s}，年龄是{a:d}，剩余金钱是{m:f}".format(n=name,a=age,m=money)
print(res2)


他的名字是lzx，年龄是22，剩余金钱是2559.600
他的名字是lzx，年龄是22，剩余金钱是2559.600000
```

String的format函数可以格式化输出，通过下标或者占位符为对应位置赋值，同时可以指定数据类型

#### 字符串查找

字符串类（str）中提供了 find 和 rfind 方法用于查找子字符串 ，返回值是查找到的子字符串所在的位置，没有找到返回-1

- str.find(sub,start,end）：在索引 start 到 end 之间查找子字符串 sub，如果找到返回最左端位置的索引，如果没有找到返回-1 
- str.rfind(sub, start,end）：与 find 方法类似，区别是如果找到返回最右端位置的索引。

```python
# 字符串查找
str5 = "This is a demo a"
print(len(str5))
print(str5[10])
print(str5.find("a"))
print(str5.rfind("a"))
print(str5.find("a",0,10))
print(str5.rfind("a",0,10))


16
d
8
15
8
8
```

#### 字符串与数字相互转换

int(),float(),bool(),str()函数

```python
# 字符串和数字转换
str6 = "9.6"
print(float(str6))
print(type(float(str6)))
num = 12.6
print(str(num))
print(type(str(num)))


9.6
<class 'float'>
12.6
<class 'str'>
```

# <a id="ysf">Python中的运算符</a>

Python 中的算术运算符用来组织整型和浮点型数据的算术运算，按照参加运算的操作数的不同可以分为一元运算符和二元运算符。

- 一元运算符
  - -：取反
- 二元运算符
  - +
  - -
  - *
  - /
  - %
  - **：幂
  - //：除了之后取整

```python
# 算数运算符

# 取反运算符 ： -
a = 16
print(-a)
# +
b = 12
print(a + b)
# -
print(a - b)
# *
print(a * b)
# /
print(a/b)
# ** 幂
print(a**b)
# // 除取整
print(a//b)

-16
28
4
192
1.3333333333333333
281474976710656
1
```

#### 关系运算符

 关系运算是比较两个表达式大小关系的运算，它的结果是布尔类型数据， 即 True 或False，关系运算符有6种

- ==
- !=
- <
- \>
- <=
- \>=

```python
# 关系运算符  除了可以用来比较int外 数组 元组之类的也可以进行比较
print(a == b)
print(a != b)
print(a < b)
print(a > b)
print(a >= b)
print(a <= b)

False
True
False
True
True
False
```

#### 逻辑运算符

逻辑运算符对布尔型变量进行运算，其结果也是布尔型

- and
- or
- not

```python
# 逻辑运算符  与Java一致 也可能造成短路现象
print(True and False)
print(True and True)
print(True or False)
print(True or True)
print(not True)

False
True
True
True
False
```

需要注意的时，与Java一致，Python里的and和or运算符也可能造成短路

#### 位运算符

位运算是以二进位（ bit）为单位进行运算的，操作数和结果都是整型数据

- ~：位反
- &：与
- |：或
- ^：异或
- \>\>：右移
- <<：左移

```python
# 位运算
c = 0b10110010
d = 0b01011110
print(~c)
print(c | d)
print(c & d)
print(c ^ d)
print(c >> 2)
print(c << 2)

-179
254
18
236
44
712
```

#### 赋值运算符

赋值运算符只是一种简写，一般用于变量自身的变化

- +=
- -=
- *=
- /=
- %=
- **=
- //=
- &=
- |=
- ^=
- \>\>=
- <<=

#### 其他运算符

除了前面介绍的主要运算符， Python 还有一些其他运算符

- 同一性测试运算符：测试是否是同一个对象（地址相同）
  - is
  - is not
- 成员测试运算符：测试一个集合中是否包含某个元素
  - in
  - not in

#### 运算符优先级

大致优先级为：

- 算数运算符
- 位运算符
- 关系运算符
- 逻辑运算符
- 赋值运算符

# <a id="kz">Python中的逻辑控制</a>


### 分支语句

- if
- if else
- if elif else

```python
# if
score = 95
if score >= 90:
    print("A")
elif score >= 80:
    print("B")
elif score >=60:
    print("C")
else:
    print("D")
    
# 三元表达式
a = 9
res = a if a > 10 else 10
print(res)
```

### 循环语句

- while
- for

```python
# while
i = 0
sum = 0
while i <= 100:
    sum += i
    i += 1
print(sum)

# for
for num in range(0,10):
    print(num)
arr = [12,85,79,0,42,16]
for num in arr:
    print(num)
```

for类似于ava里的增强for循环，一般用于遍历某个集合

### 跳转语句

- break：跳出循环
- continue：进入下一次循环
- while和for里的else：当循环全部结束之后执行一次

```python
# break
j = 0
while j <= 10:
    if j==3:
        break
    j += 1
    print(j)
print("----------------------------------------")

# continue
k = 0
while k <= 10:
    k += 1
    print(k)
    if k == 3:
        continue
print("----------------------------------------")

# while中的else
z = 0
while z <= 10:
    z += 1
else:
    print(z)
print("----------------------------------------")


# for中的else
for item in range(0,5):
    print(item)
else:
    print("xxx")
```

range函数：生成一个范围内的集合

```python
# range
for i in range(0,10,2):
    print(i)
print("----------------------------------------")
for i in range(10,-10,-2):
    print(i)
    
----------------------------------------
0
2
4
6
8
----------------------------------------
10
8
6
4
2
0
-2
-4
-6
-8
```

- 第一个参数：起始数
- 第二个参数：终止数
- 第三个参数：步长

# <a id="sjjg">Python中的数据结构</a>

## 数据结构

Python里的数据结构类似于Java的util包里提供的一些容器，是对一些基本数据结构的封装，类似于List，Set，Map等

### 元组

在介绍元组之前，首先介绍一下序列的概念。

序列是一种可迭代的、元素有序的、可以重复出现的数据结构。序列可以通过索引访问元素。序列包括的结构有：列表，字符串，元组，范围（range）和字节序列（bytes）。序列可进行的操作有索引，分片，加和乘。

元组是一种序列结构，类似于Java中的List的概念，但是元组创建之后就不可变。

#### 创建元组

元组是一种不可变序列，一旦创建就不能修改。 创建元组可以使用 tuple(） 函数或者直接用逗号","将元素分隔。

```python
# 创建元祖
a = (12,5,6.12,True)
print(a)
b = tuple([12.9,54,-8,False])
print(b)

(12, 5, 6.12, True)
(12.9, 54, -8, False)
```

tuple()函数的参数是一个可迭代对象，例如列表等

```python
# 注意情况
c = (21)
print(type(c))
d = (21,)
print(type(d))
e = ()
print(type(e))

<class 'int'>
<class 'tuple'>
<class 'tuple'>
```

当创建元组只有一个元素时，末尾的","不能省略，否则会被识别为int类型

#### 访问元组

元组作为序列可以通过下标索引访问其中的元素，也可以对其进行分片。 

```python
# 元组访问
arr = ("hello","world","Java",87,True)
print(arr[0])
print(arr[0:3])
print(arr[0:4:2])

hello
('hello', 'world', 'Java')
('hello', 'Java')
```

#### 元组拆包

Python支持将元组中的元素拆包，赋值给某些变量

```python
# 元组拆包
str1,str2,str3,num,t = arr
print(str1)
print(str2)
print(str3)
print(num)
print(t)
print("-------------------------------------")
str1,str2,*t = arr
print(str1)
print(str2)
print(t)
print("-------------------------------------")

-------------------------------------
hello
world
Java
87
True
-------------------------------------
hello
world
['Java', 87, True]
-------------------------------------
```

#### 遍历元组

一般用for循环遍历元组

```python
# 元组遍历
for item in arr:
    print(item)
# 遍历时同时获取索引
for i,item in enumerate(arr):
    print("{0}-------{1}".format(i,item))
```


### 列表

列表 (list) 也是一种序列结构，与元组不同 ，列表具有可变性，可以追加、插入、删除和替换列表中的元素。

#### 列表创建

```python
# 列表
# 列表创建
list1 = [15,89,21.5,True,"dadas"]
print(list1)
print("-------------------------------------")
list2 = list(arr)
print(list2)
print("-------------------------------------")
```

#### 列表追加

```python
# 追加元素  追加单个元素用append方法  追加另一个列表用extend方法或者+
list1.append("demo")
print(list1)
print("-------------------------------------")
list1.extend(list2)
print(list1)
print("-------------------------------------")
print(list1 + list2)
print("-------------------------------------")
```

#### 插入元素

```python
# 插入元素  insert方法可以向指定位置插入一个元素
list3 = [15,89]
list3.insert(0,"demo")
print(list3)
```

#### 替换元素

```python
# 替换元素
list3[0] = "newDemo"
print(list3)
```

#### 删除元素

```python
# 删除元素 remove删除指定元素  pop弹出一个元素 返回该元素
list3.remove(15)
print(list3)
res = list3.pop()
print(res)
print(list3)
print("-------------------------------------")
list4 = [15,78,49,326,158]
list4.pop(3)
print(list4)
```

#### 其他常用方法

- reverse()：倒置列表
- copy()：复制列表
- clear()：清除列表所有元素
- index(x,i,j)：查找i和j范围内第一次出现的x的下标
- count(x)：返回x出现的次数

```python
# 其他常用方法
list5 = [0,15,94,0,84,32,0,1]
list5.reverse()  # 倒置列表
print(list5)
print("-------------------------------------")
list6 = list5.copy()
print(list6)
print("-------------------------------------")
index = list5.index(84)  # 查找84的索引
print(index)
number = list5.count(0)   # 统计0的个数
print(number)
list5.clear()  # 清空所有元素
print(list5)
```

#### 列表推导式

Python 中有一种特殊表达式一一推导式，它可以将一种数据结构作为输入，经过过滤、计算等处理， 最后输出另一种数据结构。根据数据结构的不同可分为列表推导式、集合推导式和字典推导式。

```python
# 列表推导式
# 获得 0～9 中偶数的平方数列
list7 = []
for i in range(0,10):
    if i % 2 == 0:
        list7.append(i ** 2)
print(list7)
print("--------------------------------------")
# 用列表表达式实现
list8 = []
list8 = [i ** 2 for i in range(0,10) if i % 2 == 0]
print(list8)
```

其中后面的代码就是列表推导式，输出的结果与 for 循环是一样的。如下图所示是列表推导式语法结构，其中 in 后面的表达式是“输入序列”； for 前面的表达式是“输出表达式”，它的运算结果会保存一个新列表中： if 条件语句用来过滤输入序列，符合条件的才传递给输出表达式，“条件语句”是可以省略的，所有元素都传递给输出表达式。

![image-20200127231125711](C:\Users\67409\AppData\Roaming\Typora\typora-user-images\image-20200127231125711.png)

条件语句可以包含多个条件，例如找出 0～99 可以被 5 整除的偶数数列，实现代码如下。

```python
list9 = [i ** 2 for i in range(0,99) if i % 2 == 0 if i % 5 == 0]

```

### 集合

集合(set)是一种可迭代的、无序的、不能包含重复元素的数据结构。类似于Java中的Set。

集合又分为可变集合（set）和不可变集合（frozenset）。

#### 创建集合

```python
# 集合
# 创建可变集合  如果有重复元素会自动剔除
set1 = {15,"ads",True,89,15,True}
set2 = set(("dasd",515,12.6,False,515))
print(set1)
print("--------------------------------------")
print(set2)
print("--------------------------------------")
print(len(set2))   # 获取元素个数
set3 = {}   # 此种方式创建出来的是字典 要创建空集合 需要用set函数
print(type(set3))
set4 = set()
print(type(set4))

{89, True, 'ads', 15}
--------------------------------------
{False, 515, 12.6, 'dasd'}
--------------------------------------
4
<class 'dict'>
<class 'set'>
```

#### 修改集合

```python
# 修改可变集合
# 添加元素 如果已存在 则不能添加 不抛出异常
set4.add(True)
print(set4)
print("---------------------------------------")
set4.add(True)
print(set4)
print("---------------------------------------")
# 删除元素 若不存在则抛出错误
set4.remove(True)
print(set4)
print("----------------------------------------")
# set4.remove(True)
print("-------------------------------------")
# 删除元素 若不存在不会抛出异常
set4.discard(True)
print(set4)
print("------------------------------------------")
# 删除集合中随机一个元素 返回删除的值
set4.add(85)
set4.add(97)
set4.add("dada")
res = set4.pop()
print(res)
print(set4)
print("------------------------------------------")
# 清空集合元素
set4.clear()
print(set4)

{True}
---------------------------------------
{True}
---------------------------------------
set()
----------------------------------------
-------------------------------------
set()
------------------------------------------
97
{85, 'dada'}
------------------------------------------
set()
```

#### 遍历集合

集合是无序的，没有索引，不能通过下标访问单个元素。但可以遍历集合，访问集合每一 个元素

```python
# 遍历集合
for item in set2:
    print(item)
print("------------------------------------------")
for i,item in enumerate(set2):
    print("{0}--->{1}".format(i,item))
```

#### 不可变集合

不可变集合类型是 frozenset，创建不可变集合应使用 frozenset(）函数，不能使用大括号｛｝

```python
# 创建不可变集合
set5 = frozenset(("dada",True,False,True,5292,26.8))
print(set5)

frozenset({False, True, 5292, 'dada', 26.8})
```


#### 集合推导式

集合推导式与列表推导式类似， 区别只是输出结果是集合。

```python
# 集合推导式
set6 = {x ** 2 for x in range(100) if x % 2 == 0}
print(set6)
```

### 字典

字典（dieο 是可迭代的、可变的数据结构，通过键来访问元素。字典结构比较复杂，它是由两部分视图构成的， 一个是键（ key）视图，另一 个是值（value）视图 。键视图不能包含重复元素， 而值集合可以，键和值是成对出现的。 

字典类似于Java中的Map。

#### 创建字典

字典类型是 diet，创建字典可以使用 dict（）函数，或者用大括号｛｝将“键：值” 对括起来，“键：值”对之间用逗号分分隔。

```python
# 字典
# 创建字典
dict1 = {15:"zh",18:"qi",97.6:True}
print(dict1)
print("---------------------------------------")
dict2 = dict([(26,"dad"),(98,"dagg")])
print(dict2)
```

#### 修改字典

```python
# 修改字典
dict1[15] = "new"
print(dict1)
print("-------------------------------------")
del dict1[18]       # 删除指定key-value  不存在时抛出异常
print(dict1)
print("---------------------------------------")
value = dict1.pop(19,"default")    # 删除key-value 不存在时使用默认值
print(value)
item = dict1.popitem()   # 随机删除一组 返回删除的值
print(item)
```

#### 访问字典

```python
# 访问字典
# get方法 通过键返回值 不存在则返回默认值
dict3 = {15:"dwe",97:"dada",85:"gegr"}
print(dict3.get(15,"default"))
print(dict3.get(19,0))
print("------------------------------------")
# items方法 返回字典的所有键值对
print(dict3.items())
print("------------------------------------")
# keys 返回所有key
print(dict3.keys())
print("---------------------------------------")
# values 返回所有value
print(dict3.values())
print("-------------------------------------")
```

#### 遍历字典

```python
# 字典遍历
for key in dict3.keys():
    print(key)
print("-------------------------------------------")
for value in dict3.values():
    print(value)
print("-----------------------------------------")
for key,value in dict3.items():
    print("key:{0},value:{1}".format(key,value))
print("------------------------------------------")
```

#### 字典推导式

```python
# 字典推导式
res = {k:v for k,v in dict3.items() if k % 5 == 0}
print(res)
print("---------------------------------------")
res2 = {k for k,v in dict3.items() if v == "gegr"}
print(res2)
```

### 数据结构总结

#### Python内置函数

Python包含了以下容器的内置函数：

| 函数 | 描述 | 备注 |
| --- | --- | --- |
| len(item) | 计算容器中元素个数 | |
| del(item) | 删除变量 | del 有两种方式 |
| max(item) | 返回容器中元素最大值 | 如果是字典，只针对 key 比较 |
| min(item) | 返回容器中元素最小值 | 如果是字典，只针对 key 比较 |
| cmp(item1, item2) | 比较两个值，-1 小于/0 相等/1 大于 | Python 3.x 取消了 cmp 函数 |

**注意**

* **字符串**比较符合以下规则： "0" < "A" < "a"

#### 切片

| 描述 | Python 表达式 | 结果 | 支持的数据类型 |
| :---: | --- | --- | --- | --- |
| 切片 | "0123456789"[::-2] | "97531" | 字符串、列表、元组 |

* **切片** 使用 **索引值** 来限定范围，从一个大的 **字符串** 中 **切出** 小的 **字符串**
* **列表** 和 **元组** 都是 **有序** 的集合，都能够 **通过索引值** 获取到对应的数据
* **字典** 是一个 **无序** 的集合，是使用 **键值对** 保存数据

#### 运算符

| 运算符 | Python 表达式 | 结果 | 描述 | 支持的数据类型 |
| :---: | --- | --- | --- | --- |
| + | [1, 2] + [3, 4] | [1, 2, 3, 4] | 合并 | 字符串、列表、元组 |
| * | ["Hi!"] * 4 | ['Hi!', 'Hi!', 'Hi!', 'Hi!'] | 重复 | 字符串、列表、元组 |
| in | 3 in (1, 2, 3) | True | 元素是否存在 | 字符串、列表、元组、字典 |
| not in | 4 not in (1, 2, 3) | True | 元素是否不存在 | 字符串、列表、元组、字典 |
| > >= == < <= | (1, 2, 3) < (2, 2, 3) | True | 元素比较 | 字符串、列表、元组 |

**注意**

* `in` 在对 **字典** 操作时，判断的是 **字典的键**
* `in` 和 `not in` 被称为 **成员运算符**

##### 成员运算符

成员运算符用于 **测试** 序列中是否包含指定的 **成员**

| 运算符 | 描述 | 实例 |
| --- | --- | --- |
| in | 如果在指定的序列中找到值返回 True，否则返回 False | `3 in (1, 2, 3)` 返回 `True` |
| not in | 如果在指定的序列中没有找到值返回 True，否则返回 False | `3 not in (1, 2, 3)` 返回 `False` |

注意：在对 **字典** 操作时，判断的是 **字典的键**

#### 完整的 for 循环语法

* 在 `Python` 中完整的 `for 循环` 的语法如下：

```python
for 变量 in 集合:
    
    循环体代码
else:
    没有通过 break 退出循环，循环结束后，会执行的代码
```

# <a id="hs">Python函数</a>

程序中反复执行的代码可以封装到一个代码块中，这个代码块模仿了数学中的函数，具有函数名、参数和返回值，这就是程序中的函数。 Python中的函数很灵活，它可以在模块中 、但是在类之外定义，即函数，其作用域是当前模块；也可以在别的函数中定义，即嵌套函数，还可以在类中定义， 即方法。

之前用到的一些如len()，min()等都是python官方定义的内置函数。

### 函数定义

python作为解释性语言，函数必须先定义再调用。函数定义格式如下：

def 函数名（参数列表）：

​	函数体

​	return 返回值

```python
# 函数定义
def first_function():
    print("This is my first function")


def second_function(width,height):
    area = width * height
    return area


# 函数调用
first_function()
res = second_function(10, 22)
print(res)
```

### 函数参数

函数的参数与Java类似，但可以有默认值，python通过参数默认值和可变参数可以实现类似Java的函数重载

```python
# 参数设置默认值  python中没有Java的函数重载 不过可以通过默认值和可变参数的方式实现类似重载
def third_function(width=10, height=20):
    area = width * height
    return area


# 可变参数有两种*和**  *的参数作为元组传入  **参数作为字典传入
def four_function(*numbers, weight=1):
    total = 0.0
    for number in numbers:
        total += number
    return total * weight


# **可变参数
def fifth_function(**students):
    for key, value in students.items():
        print("{0}---->{1}".format(key, value))
        
        
res = four_function(12, 15, 79, 52.6, 143.9, weight=2)
print(res)
fifth_function(name='lzx', age=21, sex=1)
```

### 函数返回值

- 无返回值
  - 不写return语句
  - 返回None
- 有返回值
  - 返回单个
  - 返回多个
    - 返回元组之类的集合类型

### 函数变量作用域

变量可以在模块中创建，其作用域是整个模块，称为全局变量。

变量也可以在函数中创建，默认情况下其作用域是整个函数，称为局部变量。

函数中可以访问全局变量，但函数外不可访问函数中的变量，这是为了减少程序出错，不过python也提供了golbal关键字可以让函数中的变量成为全局变量。

### 生成器

在一个函数中经常使用 return 关键字返回数据，但是有时候会使用 yield 关键字返回数据。

使用 yield 关键字的函数返回的是一个生成器 （generator）对象， 生成器对象是一种可迭代对象。

 ```python
# 生成器
# 获取一个平方数列
def square(number):
    list1 = []
    for i in range(number + 1):
        list1.append(i * i)
    return list1


for item in square(5):
    print(item, end=" ")


# 获取一个平方数列
def square2(number):
    for i in range(number + 1):
        yield i * i


for item in square2(5):
    print(item, end=" ")
print("----------------------------------")
res = square2(5)
print(res.__next__())
print(res.__next__())
print(res.__next__())
 ```

yield返回的是一个可迭代对象，可以通过__next__方法依次获取元素，也可以通过for循环完成迭代。

生成器函数通过 yield 返回数据，与 return 不同的是， return 语句一次返回所有数据，函数调用结束；而 yieldi语句只返回一个元素数据，函数调用不会结束，只是暂停，直到__next__（） 方法被调 用，程序继续执行 yield 语句之后的语句代码。这个过程如下图所示：

![image-20200202225423845](C:\Users\67409\AppData\Roaming\Typora\typora-user-images\image-20200202225423845.png)

### 嵌套函数

函数可以定义在另外的函数体中，称作“嵌套函数”。

```python
# 嵌套函数
def calculate(n1, n2, opr):
    def add(a, b):
        return a + b

    def sub(a, b):
        return a - b

    if opr == '+':
        return add(n1, n2)
    elif opr == '-':
        return sub(n1, n2)
    else:
        return 0


print(calculate(10, 15, '+'))
```

在函数外不可以调用嵌套里的函数













# <a id="dx">Python面向对象</a>





# <a id="wj">Python文件操作</a>



# <a id="yc">Python异常处理</a>



# <a id="wl">Python网络编程</a>



# <a id="xc">Python多线程</a>



# <a id="txh">Python图形化界面</a>
























# <a id="mk">Python常用模块</a>

### sys模块

sys模块是系统相关模块，主要包含以下函数：

- **sys.argv**: 实现从程序外部向程序传递参数
- **sys.exit([arg])**: 程序退出，arg=0为正常退出。
- sys.stdout.write('please:'):标准输出，引出进度条的例子
- sys.getrecursionlimit() :获取最大递归层数
- sys.setrecursionlimit(1200):设置最大递归层数
- sys.getdefaultencoding(): 获取系统当前编码，一般默认为ascii。
- sys.setdefaultencoding(): 设置系统默认编码，执行dir（sys）时不会看到这个方法，在解释器中执行不通过，可以先执行reload(sys)，在执行 setdefaultencoding('utf8')，此时将系统默认编码设置为utf8。
- sys.getfilesystemencoding(): 获取文件系统使用编码方式，Windows下返回'mbcs'，mac下返回'utf-8'.
- **sys.path**: 获取指定模块搜索路径的字符串集合，可以将写好的模块放在得到的某个路径下，就可以在程序中import时正确找到。
- **sys.platform**: 获取当前系统平台。
- **sys.stdin,sys.stdout,sys.stderr**: stdin , stdout , 以及stderr 变量包含与标准I/O 流对应的流对象. 如果需要更好地控制输出,而print 不能满足你的要求, 它们就是你所需要的. 你也可以替换它们, 这时候你就可以重定向输出和输入到其它设备( device ), 或者以非标准的方式处理它们









# <a id="sjk">Python操作数据库</a>

### Python操作postgresql

- 安装Python操作pg的包：psycopg2

> pip install psycopg2

- 操作示例

```python
import psycopg2

# 创建连接对象
conn = psycopg2.connect(database="postgres", user="postgres", password="123456", host="localhost", port="5432")
# 获取游标
cur = conn.cursor()
# 执行SQL
cur.execute("select * from user_info")
# 获取结果 返回元组
res = cur.fetchall()
print(res)
# 提交事务
conn.commit()
# 关闭连接
cur.close()
conn.close()

[(1, 'Jack', '123456', '17629154002'), (2, 'Jack2', '123456', '17629154003'), (3, 'Jack3', '123456', '17629154004'), (4, 'Jack4', '123456', '17629154005'), (5, 'Jack5', '123456', '17629154006'), (6, 'unknow', '123456', '17629154008')]
```





# <a id="excel">Python操作Excel</a>

Python操作Excel的库众多，常用有以下几种：

- xlrd库：从excel中读取数据，支持xls、xlsx
- xlwt库：向excel中写入数据
- xlutils库：修改excel数据
- openpyxl：主要针对xlsx格式的excel进行读取和编辑

****

**openpyxl示例**

- 安装openpyxl：pip install openpyxl

```python


```

**xlwt示例**

- 安装xlwt：pip install xlwt

```python
import xlwt

# 创建Excel对象
excel = xlwt.Workbook()
# 添加页面
sheet = excel.add_sheet('user_info')
sheet.write(0, 0, '用户ID')
sheet.write(0, 1, '用户名')
sheet.write(0, 2, '用户密码')

users_info = [
    [1, 'Jack', '123456'],
    [2, 'Mary', '984264'],
    [3, 'Merry', '200481'],
    [4, 'Lucy', '89100']
]

# 添加用户数据
line = 1
for user in users_info:
    col = 0
    for field in user:
        sheet.write(line, col, field)
        col += 1
    line += 1

# 保存Excel
excel.save('user_infos.xls')
```

**xlrd示例**

- 安装xlrd：pip install xlrd

```python
import xlrd

# 打开excel
excel = xlrd.open_workbook('user_infos.xls')
# 获取sheet
sheet = excel.sheet_by_index(0)
# sheet = excel.sheet_by_name('user_info')
# 输出多少行 多少列
print('一共有{}行，{}列'.format(sheet.nrows, sheet.ncols))
# 获取某个位置的值
print('第二行第二列的值是{}'.format(sheet.cell(1, 1).value))
# 获取整行整列的值
print('第一行的所有值为：{}'.format(sheet.row_values(0)))
print('第一列的所有值为：{}'.format(sheet.col_values(0)))
# 输出所有值
for i in range(sheet.nrows):
    print(sheet.row_values(i))

一共有5行，3列
第二行第二列的值是Jack
第一行的所有值为：['用户ID', '用户名', '用户密码']
第一列的所有值为：['用户ID', 1.0, 2.0, 3.0, 4.0]
['用户ID', '用户名', '用户密码']
[1.0, 'Jack', '123456']
[2.0, 'Mary', '984264']
[3.0, 'Merry', '200481']
[4.0, 'Lucy', '89100']
```

**xlutils示例**

- 安装xlutils：pip install xlutils

```python
import xlrd
from xlutils import copy

# xlrd读取一个excel
excel = xlrd.open_workbook('user_infos.xls')
# xlutils复制一份之前的excel
new_excel = copy.copy(excel)
# 获取sheet
sheet = new_excel.get_sheet(0)
# 修改数据
sheet.write(1, 1, 'Rocks')
# 保存
new_excel.save('new_user_infos.xls')
```





