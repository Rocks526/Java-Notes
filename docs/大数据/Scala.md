- [Scala介绍](#jc)
- [Scala环境搭建](#hj)
- [变量与数据类型](#bl)
- [运算符](#ysf)
- [流程控制](#lckz)
- [函数式编程](#hss)
- [面向对象](#mxdx)
- [集合](#jh)
- [匹配模式](#ppms)
- [异常](#yc)
- [隐式转换](#yszh)
- [泛型](#fx)

# <a id="jc">Scala介绍</a>

- Scala简介

Scala 是 Scalable Language 的简写，是一门面向函数的编程语言，联邦理工学院洛桑（EPFL ）的Martin Odersky于2001年开始设计Scala。

- Scala特点

1. Scala是一门以java虚拟机（JVM）为目标运行环境并将面向对象和函数式编程的最佳特性结合在一起的静态类型编程语言。

> 静态类型也叫强类型，指变量的类型一旦定义，后续不可更改，例如Java，Scala，与之相对的是弱类型语言，如Python和JS，变量类型可以随意更改。

2. Scala 是一门多范式 (multi-paradigm) 的编程语言，Scala支持面向对象和函数式编程

3. Scala源代码(.scala)会被编译成Java字节码(.class)，然后运行于JVM之上，并可以调用现有的Java类库，实现两种语言的无缝对接。

4. Scala作为一门语言来看， 非常的简洁高效

5. Scala 在设计时，马丁·奥德斯基 是参考了Java的设计思想，可以说Scala是源于Java，同时马丁·奥德斯基也加入了自己的思想，将函数式编程语言的特点融合到Java中

> Scala的创始人（Martin Odersky）是编译器及编程的狂热爱好者，长时间的编程之后，希望发明一种语言，能够让写程序这样的基础工作变得高效，简单，且令人愉悦。所以当接触到Java语言后，对Java这门便携式，运行在网络，且存在垃圾回收的语言产生了极大的兴趣，所以决定将函数式编程语言的特点融合到Java中，由此发明了两种语言（Pizza & Scala） 
>
> Pizza和Scala极大地推动了Java编程语言的发展。
>
> Jdk5.0 的泛型，for循环增强, 自动类型转换等，都是从Pizza 引入的新特性。
>
> Jdk8.0 的类型推断，Lambda表达式就是从Scala引入的特性。

# <a id="hj">Scala环境搭建</a>

- Scala运行环境搭建

1. Scala需要Java运行时库，安装Scala需要首先安装JVM虚拟机并配置好，推荐安装JDK1.8

2. 安装好Java环境后，下载Scala安装包，解压，并配置环境变量

![image-20201125161326632](http://rocks526.top/lzx/image-20201125161326632.png)

- Scala的交互式运行环境

![image-20201125161501145](http://rocks526.top/lzx/image-20201125161501145.png)

> Scala也提供类似于Python的交互式运行环境，不同的是，Python是解释性语言，交互式环境其实是解释器在不断的对用户输入的内容计算并输出，而Scala运行于JVM之上，会将scala代码编译成字节码运行，因此此交互式环境其实是将指令代码快速的编译成Java字节码并在JVM加载执行，最终将执行结果输出到命令行中。

- Scala程序

编写一个名字为Hello.scala的程序，内容如下：

![image-20201125161916402](http://rocks526.top/lzx/image-20201125161916402.png)

执行方式有两种，第一种是和Java类似，先通过scalac编译成class字节码，然后运行，第二种是直接scala运行文件，本质上也是先编译成字节码再Jvm加载运行，只不过是放在内存中生成字节码。

![image-20201125162009709](http://rocks526.top/lzx/image-20201125162009709.png)

- IDEA

IDEA原生不支持Scala开发，需要安装Scala插件，安装完成后创建Maven项目，点击项目右键Add Framework Support，引入Scala支持即可。

# <a id="bl">变量与数据类型</a>

> Scala的代码风格，包括注释/空格/类名/方法名规范等基本和Java保持一致，因此不再过多介绍。
>
> 需要注意的是，Scala的单行代码可以不带分号，与Python一致。

### 变量

` var | val 变量名 [: 变量类型] = 变量值`

Scala声明一个变量的语法如上，变量的类型可以不指定，编译器会自动推断。Scala是强类型语言，变量声明后，变量类型不可变。

声明变量有var和val两种方式，var的变量可变，val的变量不可变。

> 与Java不同的是，Scala在变量声明时就需要初始化，指定值。
>
> val变量在编译成class字节码后，类属性会带final关键字，局部变量没有限制，只通过scala编译器限制，因此可以通过修改字节码跳过限制。

```scala
object Demo2 {

  def main(args: Array[String]): Unit = {

    // 变量声明不指定初始化值会报错
//    var age

    // 声明int类型的age变量 这两种方式等效
    var age = 18
    var age2:Int = 18

    // var代表可变变量  val代表不可变变量  如果是类属性，通过final限制 局部变量只能通过语法限制
    val age3 = 18
    
    age = 20
    println(age)
    
    // 编译报错
//    age3 = 20

  }

}
```

### 输入与输出

Scala输出有以下几种：

- println：与Java标准输出类似
- printf：C语言类似的占位符输出模式
- s开头的字符串：通过$直接引用变量

> Scala支持文本块

```scala
object Demo3 {

  def main(args: Array[String]): Unit = {

    var name:String = "Rocks"
    var age:Int = 22
    // 与Java的标准输出类似，编译后会变成Java的标准输出
    println(name + " " + age)

    // 与C的语法类似 通过占位符赋值
    printf("name = %s, age = %d\n", name, age)

    // 通过$引用变量，需要在字符串前加s
    println(s"name = $name, age = $age")

    // Scala支持文本块
    prnt(
      s"""
         name = $name
         age = $age
         """.stripMargin)

  }

}
```

Scala接收用户输入：

- StdIn.readLine：读取一行
- StdIn.readShort：读取一个Short
- StdIn.readDouble：读取一个Double

> Scala的输出不需要Java构造各种字节，字符，输入输出流

```scala
import scala.io.StdIn

object Demo4 {
  def main(args: Array[String]): Unit = {
    println("请输入姓名:")
    val name = StdIn.readLine()
    println("请输入年龄:")
    val age = StdIn.readShort()
    println("请输入薪水:")
    val sal = StdIn.readDouble()
    println(s"姓名:$name, 年龄:$age, 薪水:$sal")
  }
}
```

### Scala的数据类型

![image-20201206112833874](http://rocks526.top/lzx/image-20201206112833874.png)

- Scala的所有数据都是对象，都是Any的子类
- Scala中的数据类型分为两大类：数值类型(AnyVal)和引用类型(AnyRef)。
- Scala数据类型支持低精度到高精度的自动转换
- Null类似于Java中的null，Unit类似于Java中的void

### Unit && Null && Nothing

| **Unit**    | 表示无值，和其他语言中void等同。用作不返回任何结果的方法的结果类型。Unit只有一个实例值，写成()。 |
| ----------- | ------------------------------------------------------------ |
| **Null**    | null , Null 类型只有一个实例值null                           |
| **Nothing** | Nothing类型在Scala的类层级的最低端；它是任何其他类型的子类型。  当一个函数，我们确定没有正常的返回值，可以用Nothing来指定返回类型，这样有一个好处，就是我们可以把返回的值（异常）赋给其它的函数或者变量（兼容性） |

### 类型转换

- 支持隐式转换，强制转换

```scala
object Demo5 {
  def main(args: Array[String]): Unit = {
    // 类型转换
    var age:Int = 18.8.toInt
    var age2:String = 18.8 + ""
    var age3:Int = "18".toInt
  }
}
```

# <a id="ysf">运算符</a>

### 算数运算符

| 运算符 | 运算       | 范例       | 结果    |
| ------ | ---------- | ---------- | ------- |
| +      | 正号       | +3         | 3       |
| -      | 负号       | b=4; -b    | -4      |
| +      | 加         | 5+5        | 10      |
| -      | 减         | 6-4        | 2       |
| *      | 乘         | 3*4        | 12      |
| /      | 除         | 5/5        | 1       |
| %      | 取模(取余) | 7%5        | 2       |
| +      | 字符串相加 | “He”+”llo” | “Hello” |

### 关系运算符

| 运算符 | 运算     | 范例  | 结果  |
| ------ | -------- | ----- | ----- |
| ==     | 相等于   | 4==3  | false |
| !=     | 不等于   | 4！=3 | true  |
| <      | 小于     | 4<3   | false |
| >      | 大于     | 4>3   | true  |
| <=     | 小于等于 | 4<=3  | false |
| >=     | 大于等于 | 4>=3  | true  |

### 逻辑运算符

| **运算符** | **描述** | **实例**                   |
| ---------- | -------- | -------------------------- |
| **&&**     | 逻辑与   | (A && B)  运算结果为 false |
| **\|\|**   | 逻辑或   | (A \|\| B) 运算结果为 true |
| **!**      | 逻辑非   | !(A &&  B) 运算结果为 true |

### 赋值运算符

| **运算符** | **描述**                                       | **实例**                              |
| ---------- | ---------------------------------------------- | ------------------------------------- |
| **=**      | 简单的赋值运算符，将一个表达式的值赋给一个左值 | C = A + B 将 A + B 表达式结果赋值给 C |
| **+=**     | 相加后再赋值                                   | C += A 等于 C = C + A                 |
| **-=**     | 相减后再赋值                                   | C -= A 等于 C = C - A                 |
| ***=**     | 相乘后再赋值                                   | C *= A 等于 C = C * A                 |
| **/=**     | 相除后再赋值                                   | C /= A 等于 C = C / A                 |
| **%=**     | 求余后再赋值                                   | C %= A 等于 C = C % A                 |
| **<<=**    | 左移后赋值                                     | C <<= 2 等于 C = C << 2               |
| **>>=**    | 右移后赋值                                     | C >>= 2 等于 C = C >> 2               |
| **&=**     | 按位与后赋值                                   | C &= 2  等于 C = C & 2                |
| **^=**     | 按位异或后赋值                                 | C ^= 2  等于 C = C ^ 2                |
| **\|=**    | 按位或后赋值                                   | C \|= 2  等于 C = C \| 2              |

> Scala没有++和--操作，可以通过+=和-=实现。

### 位运算符

| **运算符** | **描述**       | **实例**                                                     |
| ---------- | -------------- | ------------------------------------------------------------ |
| **&**      | 按位与运算符   | (a & b) 输出结果 12 ，二进制解释： 0000 1100                 |
| **\|**     | 按位或运算符   | (a \| b) 输出结果 61 ，二进制解释： 0011 1101                |
| **^**      | 按位异或运算符 | (a ^ b) 输出结果 49 ，二进制解释： 0011 0001                 |
| **~**      | 按位取反运算符 | (~a ) 输出结果 -61 ，二进制解释： 1100 0011， 在一个有符号二进制数的补码形式。 |
| **<<**     | 左移动运算符   | a << 2 输出结果 240 ，二进制解释： 1111 0000                 |
| **>>**     | 右移动运算符   | a >> 2 输出结果 15 ，二进制解释： 0000 1111                  |
| **>>>**    | 无符号右移     | A >>>2 输出结果 15, 二进制解释: 0000 1111                    |

# <a id="lckz">流程控制</a>

### if语句

Scala的if语句Java基本一致，如下所示：

> Scala不支持三元表达式，只有和Python类似的单行if

```scala
import scala.io.StdIn

object Demo1 {
  def main(args: Array[String]): Unit = {

    println("请输入成绩")
    val grade = StdIn.readInt()

    if (grade == 100){
      println("成绩为100分")
    }else if (grade > 80 && grade <= 90){
      println("成绩大于80，小于90")
    }else{
      println("成绩小于80")
    }
    var res = 50
    res = if (res > 60) res else 60
    println(res)
  }

}
```

### for语句

- 范围循环(to模式) ==>  前后闭合，即包括起始值1和截止值10

```scala
object Demo2 {
  def main(args: Array[String]): Unit = {
    for(i <- 1 to 10){
      println(s"循环次数:$i")
    }
  }
}
======================================
循环次数:1
循环次数:2
...........................
循环次数:9
循环次数:10
```

- for循环，until模式  ==>  前闭合，即包扩起始值1，不包括截止值3

```scala
    for(i <- 1 until 3){
      println(s"循环次数:$i")
    }
=====================================================
循环次数:1
循环次数:2
```

- for循环，循环守卫  ==>  类似于Java的for里加if判断进行continue

```scala
    for (i <- 1 to 10 if i != 5) {
      println(i + "")
    }
===============  等价于以下方式  ========================
    for (i <- 1 to 10) {
      if (i != 5){
        println(i + "")
      }
    }
```

- for循环，增加步长

```scala
    for (i <- 1 to 10 by 2) {
      println("i=" + i)
    }
==================================================
i=1
i=3
i=5
i=7
i=9
```

- 嵌套for循环

```scala
      for (i <- 1 to 3; j<- 1 to 3){
        println(s"i=$i, j=$j")
      }
======================  等价于  ==========================
        for (i <- 1 to 3) {
            for (j <- 1 to 3) {
                println("i =" + i + " j=" + j)
            }
        }
```

- for循环引入变量  ==>  通过变量i计算变量j

```scala
    for (i <- 1 to 3; j = i + 2){
      println(s"i=$i, j=$j")
    }
===============================================
i=1, j=3
i=2, j=4
i=3, j=5
==============================================
# 当圆括号里的条件表达式超过1条时，需要加；  或者改写成{}模式
    for {
      i <- 1 to 3
      j = i + 2
    }{
      println(s"i=$i, j=$j")
    }
```

- 通过for循环构造集合

```scala
      val res = for(i <- 1 to 10) yield i
      println(res)
=================================================
Vector(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
```

### while循环

> while循环语法与Java基本一致。
>
> 由于while中没有返回值，所以当要用该语句来计算并返回结果时，就不可避免的使用变量，而变量需要声明在while循环的外部，那么就等同于循环的内部对外部的变量造成了影响，也就违背了函数式编程的重要思想（输入=>函数=>输出，不对外界造成影响），所以scala不推荐使用，而是推荐使用for循环。

```scala
object Demo3 {
  def main(args: Array[String]): Unit = {
    var i = 0
    while (i < 10){
      println(s"循环次数i=$i")
      i = i + 1
    }
  }
}
```

### do While循环

> 与Java保持一致。

```scala
object Demo4 {
  def main(args: Array[String]): Unit = {
    var i = 0
    do {
      println(s"循环次数i = $i")
      i = i + 1
    }while(i < 10)
  }
}
```

### While循环中断

> Scala内置控制结构特地去掉了break和continue，是为了更好的适应函数式编程，推荐使用函数式的风格解决break和continue的功能，而不是一个关键字。scala中使用breakable控制结构来实现break和continue功能。
>
> 注意breakable的导包

```scala
# 循环10次 当i=5时跳出循环
object Demo5 {
  def main(args: Array[String]): Unit = {
    var i = 0
    breakable {
      while (i < 10){
        println(s"i = $i")
        i = i + 1
        if (i == 5){
          break()
        }
      }
    }
  }
}

# 当i=5时跳过
import util.control.Breaks._

object Demo6 {
    def main(args: Array[String]): Unit = {
        var i = 0
        while (i < 10){
            breakable {
                i = i+ 1
                if (i == 5){
                    break()
                } else {
                    println(s"i = $i")
                }
            }
        }
    }
}
```

# <a id="hss">函数式编程</a>

### 函数的定义

![image-20201207100059214](http://rocks526.top/lzx/image-20201207100059214.png)

```scala
object Demo1 {
    
    // 函数定义
    def first_function(arg:String): Unit = {
        println(s"传入的参数是:$arg")
    }
    
    def main(args: Array[String]): Unit = {
        // 函数可以定义在main方法内 ==> 支持嵌套定义
        def second_function(arg:String): Unit = {
            println(s"传入的参数是:$arg")
        }
        first_function("Hello World")
        second_function("Hello World2")
    }
}
```

函数和方法的区别：

- 为完成某一功能的程序指令（语句）的集合，称为函数。
- 类中的函数称之方法。
- 函数没有重载和重写的概念；方法可以进行重载和重写
- scala中函数可以嵌套定义

```scala
object Demo2 {
    def main(args: Array[String]): Unit = {
        // 函数1：无参，无返回值
        def test(): Unit ={
            println("无参，无返回值")
        }
        test()
    
        // 函数2：无参，有返回值
        def test2():String={
            return "无参，有返回值"
        }
        println(test2())
    
        // 函数3：有参，无返回值
        def test3(s:String):Unit={
            println(s)
        }
        test3("demo")
    
        // 函数4：有参，有返回值
        def test4(s:String):String={
            return s+"有参，有返回值"
        }
        println(test4("hello "))
    
    
        // 函数5：多参，无返回值
        def test5(name:String, age:Int):Unit={
            println(s"$name, $age")
        }
        test5("rocks",22)
    }
}
```

### 函数的参数

- Scala支持可变参数
- 如果参数列表中存在多个参数，那么可变参数一般放置在最后
- Scala的函数参数支持默认值
- Scala的函数支持带名参数

```scala
object Demo3 {
    def main(args: Array[String]): Unit = {
        // （1）可变参数
        def test( s : String* ): Unit = {
            println(s)
        }
    
        // 有输入参数：输出 Array
        test("Hello", "Scala")
    
        // 无输入参数：输出List()
        test()
    
        // (2)如果参数列表中存在多个参数，那么可变参数一般放置在最后
        def test2( name : String, s: String* ): Unit = {
            println(name + "," + s)
        }
        /*
        可变参数一般放置在最后
        def test2( s: String*,name : String ): Unit = {
            println(name + "," + s)
        }
        */
        test2("Rocks", "demo")
    
        // (3)参数默认值
        def test3( name : String, age : Int = 30 ): Unit = {
            println(s"$name, $age")
        }
    
        // 如果参数传递了值，那么会覆盖默认值
        test3("Rocks", 20)
    
        // 如果参数有默认值，在调用的时候，可以省略这个参数
        test3("Rocks")
    
    
        def test4( sex : String = "男", name : String ): Unit = {
            println(s"$name, $sex")
        }
    
        // scala函数中参数传递是，从左到右
        // 一般情况下，将有默认值的参数放置在参数列表的后面
        //        test4("Rocks")
    
        //（4）带名参数
        test4(name="Rocks")
    }
}
```

### 函数简化

- return可以省略，Scala会使用函数体的最后一行代码作为返回值
- 返回值类型如果能够推断出来，那么可以省略
- 如果函数体只有一行代码，可以省略花括号
- 如果函数无参，则可以省略小括号。若定义函数时省略小括号，则调用该函数时，也需省略小括号；若定时函数时未省略，则调用时，可省可不省。
- 如果函数明确声明Unit，那么即使函数体中使用return关键字也不起作用
- Scala如果想要自动推断无返回值，可以省略等号
- 如果不关心名称，只关系逻辑处理，那么函数名（def）可以省略
- 如果函数明确使用return关键字，那么函数返回就不能使用自行推断了，需要声明返回值类型

```scala
// 函数简化
object Demo4 {
    def main(args: Array[String]): Unit = {
        // 0）函数标准写法
        def f1( s : String ): String = {
            return s + " Rocks"
        }
        println(f1("Hello"))
    
        // 至简原则:能省则省
    
        //（1） return可以省略,scala会使用函数体的最后一行代码作为返回值
        def f2( s : String ): String = {
            s + " Rocks"
        }
        println(f2("Hello"))
    
        // 如果函数名使用return关键字，那么函数就不能使用自行推断了,需要声明返回值类型
        /*
        def f22(s:String)={
            return "jinlian"
        }
        */
    
        //（2）返回值类型如果能够推断出来，那么可以省略
        def f3( s : String ) = {
            s + " Rocks"
        }
    
        println(f3("Hello"))
    
        //（3）如果函数体只有一行代码，可以省略花括号
        //def f4(s:String) = s + " Rocks"
        //def f4(s:String) = "Rocks"
        def f4() = " Rocks"
    
        // 如果函数无参，但是声明参数列表，那么调用时，小括号，可加可不加。
        println(f4())
        println(f4)
    
        //（4）如果函数没有参数列表，那么小括号可以省略,调用时小括号必须省略
        def f5 = "Rocks"
        // val f5 = "Rocks"
    
        println(f5)
    
        //（5）如果函数明确声明unit，那么即使函数体中使用return关键字也不起作用
        def f6(): Unit = {
            return "abc"
        }
        println(f6())
    
        //（6）scala如果想要自动推断无返回值,可以省略等号
        // 将无返回值的函数称之为过程
        def f7(): Unit = {
            "Rocks"
        }
        println(f7())
    
        //（7）如果不关心名称，只关系逻辑处理，那么函数名（def）可以省略
        //()->{println("xxxxx")}
        val f = (x:String)=>{"Rocks"}
    
        // 万物皆函数 : 变量也可以是函数
        println(f("Rocks"))
    
        //（8）如果函数明确使用return关键字，那么函数返回就不能使用自行推断了,需要声明返回值类型
        def f8() :String = {
            return "Rocks"
        }
        println(f8())
    }
}
```

### 参数默认值

> 和Python类似，Scala支持函数的参数可以设置默认值

```scala
/**
 * 函数默认值
 */
object Demo9 {

  def main(args: Array[String]): Unit = {

    def test(name :String = "rocks"): Unit ={
      println(name)
    }

    test()
    test("Rocks526")

    def test2(name : String = "Rocks", age : Int): Unit ={
      println(s"Name=$name, age=$age")
    }

//    test2(22)  由于参数值会自动从左往右填充 因此22会当成name解析 由于age没有值 所以报错
    test2(age=22)
  }

}
```

### 高阶函数

> Scala是完全面向函数的语言，函数可以做任何事，包括函数赋值给变量，函数作为函数的参数，函数作为函数的返回值等等。

> 在Scala中，将参数为函数的函数称为高阶函数

```scala
// 高阶函数
object Demo5 {
    def main(args: Array[String]): Unit = {
        //高阶函数————函数作为参数
        def calculator(a: Int, b: Int, operator: (Int, Int) => Int): Int = {
            operator(a, b)
        }
    
        //函数————求和
        def plus(x: Int, y: Int): Int = {
            x + y
        }
    
        //方法————求积
        def multiply(x: Int, y: Int): Int = {
            x * y
        }
    
        //函数作为参数
        println(calculator(2, 3, plus))
        println(calculator(2, 3, multiply))
    }
}
```

### 匿名函数

> 没有名字的函数就是匿名函数，可以直接通过**函数字面量（**                                **表达式）**来设置匿名函数，函数字面量定义格式如下。

![image-20201207103233937](http://rocks526.top/lzx/image-20201207103233937.png)

```scala
// 匿名函数
object Demo6 {
    //高阶函数————函数作为参数
    def calculator(a: Int, b: Int, operator: (Int, Int) => Int): Int = {
        operator(a, b)
    }
    
    //函数————求和
    def plus(x: Int, y: Int): Int = {
        x + y
    }
    
    def main(args: Array[String]): Unit = {
        
        //函数作为参数
        println(calculator(2, 3, plus))
        
        //匿名函数作为参数
        println(calculator(2, 3, (x: Int, y: Int) => y - x))
        
        //匿名函数简写形式
        println(calculator(2, 3, _ - _))
    }
}
```

### 函数柯里化和闭包

> 函数柯里化：将一个接收多个参数的函数转化成一个接受一个参数的函数过程，可以简单的理解为一种特殊的参数列表声明方式。
>
> 闭包：一个函数在实现逻辑时，将外部的变量引入到函数的内部，改变了这个变量的生命周期，称之为闭包。
>
> 通过闭包实现函数柯里化。

```scala
object Demo7 {
    
    val sum = (x: Int, y: Int, z: Int) => x + y + z
    
    val sum1 = (x: Int) => {
        y: Int => {
            z: Int => {
                x + y + z
            }
        }
    }
    
    val sum2 = (x: Int) => (y: Int) => (z: Int) => x + y + z
    
    def sum3(x: Int)(y: Int)(z: Int) = x + y + z
    
    def main(args: Array[String]): Unit = {
        // 外部变量
        var x: Int = 10
    
        // 闭包
        def f(x: Int, y: Int): Int = {
            x + y
        }
        // 匿名函数
        sum(1, 2, 3)
        sum1(1)(2)(3)
        sum2(1)(2)(3)
        sum3(1)(2)(3)
    }
}
```

### 惰性函数

> 当**函数返回值被声明为lazy时**，函数的**执行将被推迟**，直到我们**首次对此取值，该函数才会执行**。这种函数我们称之为惰性函数。

```scala
object Demo8 {
    
    def main(args: Array[String]): Unit = {
        
        lazy val res = sum(10, 30)
        println("----------------")
        println("res=" + res)
    }
    
    def sum(n1: Int, n2: Int): Int = {
        println("sum被执行。。。")
        return n1 + n2
    }
    
}
=========================================================
----------------
sum被执行。。。
res=40
```

# <a id="mxdx">面向对象</a>











# <a id="jh">集合</a>





# <a id="ppms">匹配模式</a>





# <a id="yc">异常</a>





# <a id="yszh">隐式转换</a>





# <a id="fx">泛型</a>







