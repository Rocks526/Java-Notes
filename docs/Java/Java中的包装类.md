# 一：Java包装类介绍

### 1.1 Java包装类介绍

Java早期为了`保持和其他语言的一致性，内置了八种基础数据类型`，但为了`符合Java面向对象的思想，Java对这八种基本数据类型进行了包装，形成了对应的包装类`，他们之间的对应关系如下：

| 基本类型 | 包装类型  | 长度  |
| -------- | --------- | ----- |
| byte     | Byte      | 1字节 |
| short    | Short     | 2字节 |
| int      | Integer   | 4字节 |
| long     | Long      | 8字节 |
| float    | Float     | 4字节 |
| double   | Double    | 8字节 |
| boolean  | Boolean   | 1字节 |
| char     | Character | 2字节 |

### 1.2 包装类和基本类型的区别

在Java语言中，对象的分配都是在堆上，方法执行的虚拟机栈通过引用去寻找堆上的对象进行操作。

因此对于基本类型，是直接分配在栈帧的局部变量表里，而包装类型是分配在堆里，局部变量表里存储对象的引用。

因此基本类型相比包装类型，性能会更好一些，在一些追求极致性能的场景，可以使用基本类型，例如Netty。反之，在绝大多数场景下，都可以优先使用包装类型，`通过少量的性能损耗换取编码上的效率提升还是值得的`。

### 1.3 Java包装类和基本类型的转换

Java的包装类型都提供了和基本类型相互转换的方法，基本都是`valueOf()`和`xxxxValue()`系列，示例如下：

```java
package com.lizhaoxuan.lang;

/**
 * 包装类型测试
 * @author lizhaoxuan
 */
public class WrapperClassTest {

    public static void main(String[] args) {
        // int
        int x = 1;
        Integer wrapperX = Integer.valueOf(x);
        int x2 = wrapperX.intValue();
        System.out.println(x + "," + wrapperX + "," + x2);

        // long
        long v = 2l;
        Long wrapperV = Long.valueOf(v);
        long v2 = wrapperV.longValue();
        System.out.println(v + "," + wrapperV + "," + v2);

        // double
        double m = 0.2f;
        Double wrapperM = Double.valueOf(m);
        double m2 = wrapperM.doubleValue();
        System.out.println(m + "," + wrapperM + "," + m2);

        // float
        float n = 0.8f;
        Float wrapperN = Float.valueOf(n);
        float n2 = wrapperN.floatValue();
        System.out.println(n + "," + wrapperN + "," + n2);

        // bool
        boolean p = false;
        Boolean wrapperP = Boolean.valueOf(p);
        boolean p2 = wrapperP.booleanValue();
        System.out.println(p + "," + wrapperP + "," + p2);

        // str
        char o = 'p';
        Character wrapperO = Character.valueOf(o);
        char o2 = wrapperO.charValue();
        System.out.println(o + "," + wrapperO + "," + o2);

        // short
        short c = 2;
        Short wrapperC = Short.valueOf(c);
        short c2 = wrapperC.shortValue();
        System.out.println(c + "," + wrapperC + "," + c2);

        // byte
        byte z = 'm';
        Byte wrapperZ = Byte.valueOf(z);
        byte z2 = wrapperZ.byteValue();
        System.out.println(z + "," + wrapperZ + "," + z2);
    }

}
```

### 1.4 Java包装类的缓存机制

在介绍包装类的缓存机制之前，先看下下面的测试代码：

```java
    private static void wrapperCache() {
        System.out.println("=================[wrapperCache]=======================");
        int v1 = 1;
        int v2 = 288;
        Integer v3 = Integer.valueOf(v1);
        Integer v4 = Integer.valueOf(v2);
        Integer v5 = Integer.valueOf(v1);
        Integer v6 = Integer.valueOf(v2);
        System.out.println(v1 + "," + v2 + "," + v3 + "," + v4 + "," + v5 + "," + v6);  // 1,288,1,288,1,288
        System.out.println(v3 == v5);   // true
        System.out.println(v4 == v6);   // false
    }
```

Java中的每个对象都是独一无二的，等于号计算的是对象的引用地址，不同的对象应该都不一样。在上面的代码中，v3==v5返回的确实true。

这其实就是Java包装类的缓存机制，`Java包装类在加载的时候，会一次性创建出一些常用范围内的对象`，在执行valueOf()方法时，`先从创建好的对象里去获取，获取不到再创建新的对象`，这就是v3和v5是同一个对象，而v4和v6不是同一个对象的原因，Integer的缓存范围为[-128,127)。

注：其他包装类型也有类似的缓存，具体的范围参考Java包装类型源码分析。

# 二：自动拆箱装箱机制

### 4.1 自动拆箱装箱介绍

在Jdk1.5中，为了减少开发人员的工作，Java提供了自动拆装箱功能：

- 自动装箱：将基本数据类型自动转化为对应的包装类
- 自动拆箱：将包装类自动转化成对应的基本数据类型

例如如下代码，就使用了自动装箱、拆箱机制：

```java
    private static void autoWrapper() {
        System.out.println("=================[autoWrapper]=======================");
        // int
        int x = 1;
        Integer wrapperX = x;
        int x2 = wrapperX;
        System.out.println(x + "," + wrapperX + "," + x2);

        // long
        long v = 2l;
        Long wrapperV = v;
        long v2 = wrapperV;
        System.out.println(v + "," + wrapperV + "," + v2);

        // double
        double m = 0.2f;
        Double wrapperM = m;
        double m2 = wrapperM;
        System.out.println(m + "," + wrapperM + "," + m2);

        // float
        float n = 0.8f;
        Float wrapperN = n;
        float n2 = wrapperN;
        System.out.println(n + "," + wrapperN + "," + n2);

        // bool
        boolean p = false;
        Boolean wrapperP = p;
        boolean p2 = wrapperP;
        System.out.println(p + "," + wrapperP + "," + p2);

        // str
        char o = 'p';
        Character wrapperO = o;
        char o2 = wrapperO;
        System.out.println(o + "," + wrapperO + "," + o2);

        // short
        short c = 2;
        Short wrapperC = c;
        short c2 = wrapperC;
        System.out.println(c + "," + wrapperC + "," + c2);

        // byte
        byte z = 'm';
        Byte wrapperZ = z;
        byte z2 = wrapperZ;
        System.out.println(z + "," + wrapperZ + "," + z2);
    }
```

### 4.2 自动拆箱装箱原理

自动拆箱和装箱是Java提供的一种语法糖，由编译器进行处理，帮助我们实现原本的逻辑。

- 自动装箱都是通过包装类的valueOf()方法实现
- 自动拆箱都是通过包装类对象xxxValue方法实现（如intValue）

### 4.3 自动拆箱装箱应用

- 将基本类型放入集合中

集合类中都是对象类型，但是我们添加基本数据类型也不会报错，是因为Java给我们做了自动装箱。

- 包装类型和基本类型的大小比较

包装类与基本数据类型进行比较运算，先将包装类进行拆箱成基本数据类型，然后比较。

- 包装类型的运算

对两个包装类型进行算术运算运算，会将包装类型自动拆箱为基本类型进行。

- 三目运算符的使用

例如`falg? N + 1: N`这种三目运算符，会自动转为基本类型进行计算。

- 函数参数与返回值

```java
//自动拆箱
public int getNum1(Integer num) {
 return num;
}
//自动装箱
public Integer getNum2(int num) {
 return num;
}
```