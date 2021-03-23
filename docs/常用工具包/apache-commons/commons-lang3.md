# 一：Commons lang3介绍

### 1.1 Commons lang3简介

Apache Commons lang3包是对Jdk的增强，包括字符串、随机数、日期、异常等等多个方面。

与Apache Commons lang包相比，lang3包基于Jdk1.5，支持泛型等众多Jdk新特性。

### 1.2 Commons lang3的Maven依赖

```xml
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-lang3</artifactId>
            <version>3.9</version>
        </dependency>
```

# 二：Commons lang3常用API

### 2.1 Commons lang3功能介绍

![这里写图片描述](http://rocks526.top/lzx/20180906173755813)

Apache Commons lang3包主要包括以上功能包，常用的工具类有：

- StringUtils：字符串处理工具
- RandomUtils：随机数生成工具
- RandomStringUtils：随机字符串工具
- NumberUtils：数字相关工具
- DateUtils：日期相关工具
- ArrayUtils：数组相关工具
- ObjectUtils：对象相关工具

### 2.2 代码示例

- StringUtils

```java
package com.rocks.commons.lang3;

import org.apache.commons.lang3.StringUtils;

import java.util.Arrays;

/**
 * StringUtils示例代码
 * @author lizhaoxuan
 */
public class StringUtilsDemo {

    public static void main(String[] args) {

        // 判断字符串是否为 null 空串 空白字符
        System.out.println(StringUtils.isBlank(" "));
        System.out.println(StringUtils.isNotBlank(" "));

        // 判断字符串是否为 null 空串
        System.out.println(StringUtils.isEmpty(""));
        System.out.println(StringUtils.isNotEmpty(" "));

        // 判断字符串是否是数字 不忽略空格
        System.out.println(StringUtils.isNumeric(" 23"));
        // 判断字符串是否是数字 忽略空格
        System.out.println(StringUtils.isNumericSpace(" 23 "));

        // 判断字符串是否是希腊字母 不忽略空格
        System.out.println(StringUtils.isAlpha("asd"));
        System.out.println(StringUtils.isAlpha(" asd"));
        System.out.println(StringUtils.isAlpha("dsadw-cev"));
        System.out.println(StringUtils.isAlpha("dwdasd9eve"));
        // 判断字符串是否是希腊字母 忽略空格
        System.out.println(StringUtils.isAlphaSpace(" asd "));

        // 判断是否全部小写字母
        System.out.println(StringUtils.isAllLowerCase("dasdcsdd"));
        System.out.println(StringUtils.isAllLowerCase("cwdveW"));
        System.out.println(StringUtils.isAllLowerCase("dsadcd83"));
        System.out.println(StringUtils.isAllLowerCase("dasdacf;"));

        // 判断是否全部大写
        System.out.println(StringUtils.isAllUpperCase("WDASCASDA09"));
        System.out.println(StringUtils.isAllUpperCase("DQDWEDVWE;"));
        System.out.println(StringUtils.isAllUpperCase("DEAFAFASD DSAD"));

        // 判断字符串是否包含 区分大小写
        System.out.println(StringUtils.contains("demo1demo2","demo2"));
        System.out.println(StringUtils.contains("demo1","m"));
        System.out.println(StringUtils.contains("deMo1","m"));
        // 判断字符串是否包含 不区分大小写
        System.out.println(StringUtils.containsIgnoreCase("deMo1","m"));
        // 判断字符串是否包含可变参数中任意一个
        System.out.println(StringUtils.containsAny("demo1demo2demo3","demo","demo2"));



        // 去掉字符串前后空格
        String trim = StringUtils.trim(" dsadas");
        String trim1 = StringUtils.trim(null);
        System.out.println(trim + ":" + trim1);

        // 判断字符串是否相等
        StringUtils.equals("demo","Demo");
        StringUtils.equalsIgnoreCase("demo","Demo");
        StringUtils.equalsAny("demo3","demo1","demo2","demo3");
        StringUtils.equalsAny("demo3","Demo3","demo2");

        // 从指定位置开始查找子串
        StringUtils.indexOf("This is a string","is",0);
        StringUtils.indexOfIgnoreCase("This is a String","Is",0);
        StringUtils.indexOfAny("This is a String","Is","a","string","h");

        // 大小写转换
        String demo = StringUtils.upperCase("demo");
        String demo1 = StringUtils.lowerCase("Demo");
        System.out.println(demo + ":" + demo1);

        // 首字母转大写
        String spark = StringUtils.capitalize("spark");
        System.out.println(spark);

        // 将字符串前后用空格填充 使其长度达到size 如果str长度本身大于size 不做填充
        String demo2 = StringUtils.center("demo", 10);
        String demo3 = StringUtils.center("demo", 3);
        System.out.println(demo2 + ":" + demo3);

        // 字符串比较
        StringUtils.compare("demo1","Demo2");
        StringUtils.compareIgnoreCase("demo1","Demo2");

        // 查找某个字符串出现的次数
        int i = StringUtils.countMatches("This is a String", "is");
        int i1 = StringUtils.countMatches("This is a String", "i");
        System.out.println(i + ":" + i1);

        // 将列表里的所有字符串用分隔符拼接 默认分隔符为null
        String join = StringUtils.join("demo1", "demo2", "demo3");
        String join1 = StringUtils.join(Arrays.asList("demo1", "demo2", "demo3"), ",");
        System.out.println(join + " : " + join1);

        // 当字符串为null或空串或空格时返回默认值
        String aDefault = StringUtils.defaultIfBlank(null, "default");
        String s = StringUtils.defaultIfBlank(" ", "default");
        System.out.println(aDefault + ":" + s);
        // 为null或空串时返回默认值
        String aDefault1 = StringUtils.defaultIfEmpty("", "default");
        String s1 = StringUtils.defaultIfEmpty(" ", "default");
        System.out.println(aDefault1 + ":" + s1);
        // 为null时返回默认值
        String s2 = StringUtils.defaultString("dem", "default");
        System.out.println(s2);

    }

}
```

- RandomUtils

```java
package com.rocks.commons.lang3;

import org.apache.commons.lang3.RandomUtils;

/**
 * RandomUtils示例代码
 * @author lizhaoxuan
 */
public class RandomUtilsDemo {

    public static void main(String[] args) {

        // 生成 0-Integer.MAX_VALUE 的随机int
        System.out.println(RandomUtils.nextInt());
        // 生成指定范围的随机int  左闭右开
        System.out.println(RandomUtils.nextInt(0,10));

        // 生成随机Boolean
        System.out.println(RandomUtils.nextBoolean());

        // 生成 0-Long.MAX_VALUE 的随机long
        System.out.println(RandomUtils.nextLong());
        // 生成指定范围的随机long 左闭右开
        System.out.println(RandomUtils.nextLong(0,100));

        // 生成指定个数的字节数组
        System.out.println(RandomUtils.nextBytes(10));

        // 生成随机Double
        System.out.println(RandomUtils.nextDouble());
        System.out.println(RandomUtils.nextDouble(0,100.0));

        // 生成随机float
        System.out.println(RandomUtils.nextFloat());
        System.out.println(RandomUtils.nextFloat(0,100.0f));

    }

}
```

- RandomStringUtils

```java
package com.rocks.commons.lang3;

import org.apache.commons.lang3.RandomStringUtils;

/**
 * RandomStringUtils代码示例
 * @author lizhaoxuan
 */
public class RandomStringUtilsDemo {

    public static void main(String[] args) {

        // 创建长度为指定个数的随机字符串 将从所有字符集中选择字符 不含字母和数字
        System.out.println(RandomStringUtils.random(10));
        // 创建长度为指定个数的随机字符串 将从字母或数字里选择
        // 第二个参数是代表是否选择字母 第三个参数代表是否选择数字 如果都为false 则等同于random(10)
        System.out.println(RandomStringUtils.random(10,true,true));
        // 创建长度为指定个数的随机字符串 字符从后面的可变参数中选取
        System.out.println(RandomStringUtils.random(10,'a','b','c'));
        // 创建长度为指定个数的随机字符串 字符从后面的字符串中选取
        System.out.println(RandomStringUtils.random(10,"spark flink"));

        // 创建指定长度的随机字符串 字符从(a-zA-Z)里选择 等同于random(10,true,false)
        System.out.println(RandomStringUtils.randomAlphabetic(10));
        // 创建指定长度范围的随机字符串 字符从(a-zA-Z)里选择
        System.out.println(RandomStringUtils.randomAlphabetic(3,10));

        // 创建指定长度的随机字符串 字符从字母和数字里选择 等同于random(10,true,true)
        System.out.println(RandomStringUtils.randomAlphanumeric(10));
        // 创建指定长度范围的随机字符串 字符从字母和数字里选择
        System.out.println(RandomStringUtils.randomAlphanumeric(3,10));

        // 随机字符将从 ASCII 码值介于 [32,126] 之间的字符集中选择，等价于：random(count, 32, 127, false, false)
        System.out.println(RandomStringUtils.randomAscii(10));
        System.out.println(RandomStringUtils.randomAscii(3,10));

        // 随机字符从所有可见的 ASCII 字符中选择，即除空格和控制字符外的任何内容，等价于：random(count, 33, 126, false, false)
        System.out.println(RandomStringUtils.randomGraph(10));
        System.out.println(RandomStringUtils.randomGraph(3,10));

        // 创建长度为指定字符数的随机字符串，随机字符将从数字字符集中选择。
        System.out.println(RandomStringUtils.randomNumeric(10));
        System.out.println(RandomStringUtils.randomNumeric(3,10));

    }

}
```

- ObjectUtils

```java
package com.rocks.commons.lang3;

import org.apache.commons.lang3.ObjectUtils;

import java.math.BigDecimal;
import java.util.Arrays;
import java.util.Date;
import java.util.HashMap;


/**
 * ObjectUtils代码示例
 * @author lizhaoxuan
 */
public class ObjectUtilsDemo {

    public static void main(String[] args) {

        // 检查可变参数的所有元素是否都不为null
        System.out.println(ObjectUtils.allNotNull("demo",1,new Object()));
        // 检查可变参数的所有元素是否有不是null的值
        System.out.println(ObjectUtils.anyNotNull("demo",null));

        // 对象克隆 必须继承Cloneable接口 重写clone方法
        User user = new User().setId(1L).setUsername("Rocks").setPassword("123456")
                .setAge(22).setMoney(new BigDecimal(20.0)).setCreateTime(new Date())
                .setProduct(new Product().setName("product1").setDesc("this is a product"))
                .setExtendInfos(Arrays.asList("extends1","extends2"));
        User clone = ObjectUtils.clone(user);
        System.out.println(clone);
        // 对象克隆 先调clone 如果返回为null  则返回原对象
        System.out.println(ObjectUtils.cloneIfPossible(user));

        // 比较  如果 c1 < c2,则返回负数；如果 c1 > c2,则返回正数；如果 c1 = c2,则返回 0
        System.out.println(ObjectUtils.compare("Test","test"));
        // 第三个参数为 元素为null时代表最大还是最小
        System.out.println(ObjectUtils.compare("Test",null,true));

        // 如果对象为null 返回默认值
        System.out.println(ObjectUtils.defaultIfNull(null,""));

        // 返回数组中不是null的第一个值
        System.out.println(ObjectUtils.firstNonNull(null,"","demo"));

        // 检查对象是否为空 支持：CharSequence、Array、Collection、Map
        System.out.println(ObjectUtils.isEmpty(""));
        System.out.println(ObjectUtils.isEmpty(" "));
        System.out.println(ObjectUtils.isEmpty(new Object()));
        System.out.println(ObjectUtils.isEmpty(new HashMap<>()));
        // 检查对象是否不为空
        System.out.println(ObjectUtils.isNotEmpty(Arrays.asList(null,"")));

        // 获取最大 最小 中位数
        System.out.println(ObjectUtils.max(1,2,-3));
        System.out.println(ObjectUtils.min(-1,2,3));
        System.out.println(ObjectUtils.median(1,-2,3));
        System.out.println(ObjectUtils.median(1,-2,3,0));

    }

}
```

- NumberUtils

```java
package com.rocks.commons.lang3;

import org.apache.commons.lang3.math.NumberUtils;

/**
 * NumberUtils示例代码
 * @author
 */
public class NumberUtilsDemo {

    public static void main(String[] args) {

        // 内置常量 包含各个包装类型的0 1 -1 其他类似的不再演示
        System.out.println(NumberUtils.LONG_ZERO);
        System.out.println(NumberUtils.LONG_ONE);
        System.out.println(NumberUtils.LONG_MINUS_ONE);

        // 比较
        System.out.println(NumberUtils.compare(0,9));
        System.out.println(NumberUtils.compare(9L,1119L));

        // 将字符串转成 BigInteger/BigDecimal/Double等，支持 10进制，十六进制（以0X或者#开头）、8进制（以0开头）
        // 如果 str 为 null，则返回 null。如果转换错误，则抛出 NumberFormatException
        System.out.println(NumberUtils.createBigInteger("986722"));
        System.out.println(NumberUtils.createBigDecimal("321312.343"));
        System.out.println(NumberUtils.createDouble("2321.44"));

        // 判断字符串是否是有效数字 支持16进制、8进制、10进制、正数负数、科学计数法
        System.out.println(NumberUtils.isCreatable("8.788006e+05"));

        // 最大值 最小值
        System.out.println(NumberUtils.max(1,2,3,5));
        System.out.println(NumberUtils.min(0.4f,3,89L));

        // 字符串转Long Double等 支持默认值
        System.out.println(NumberUtils.toInt("2",0));
        System.out.println(NumberUtils.toLong("2321",0L));

    }

}
```

- ArrayUtils

```java
package com.rocks.commons.lang3;

import org.apache.commons.lang3.ArrayUtils;

/**
 * ArrayUtils代码示例
 * @author lizhaoxuan
 */
public class ArrayUtilsDemo {

    public static void main(String[] args) {

        // 创建数组
        System.out.println(ArrayUtils.toArray("demo1","demo2","demo3"));

        // 判断两个数组是否相等
        String[] arr1 = ArrayUtils.toArray("demo1","demo2","demo3");
        String[] arr2 = ArrayUtils.toArray("demo1","demo2","demo3",null);
        // arr2多一个null元素 不相等 没有null相等
        System.out.println(ArrayUtils.isEquals(arr1,arr2));

        // 判断是否是空数组
        String[] arr3 = new String[]{};
        System.out.println(ArrayUtils.isEmpty(arr3));

        // 判断数组是否包含某个元素
        System.out.println(ArrayUtils.contains(arr1,"demo2"));

        // 二维数组转换map
        String[][] arr4 = new String[][]{
                { "RED", "#FF0000" },
                { "GREEN", "#00FF00" },
                { "BLUE", "#0000FF" }
        };
        System.out.println(ArrayUtils.toMap(arr4));
    }

}
```

- DateUtils

```java
package com.rocks.commons.lang3;

import org.apache.commons.lang3.time.DateUtils;

import java.text.ParseException;
import java.util.Calendar;
import java.util.Date;

/**
 * DateUtils代码示例
 * @author lizhaoxuan
 */
public class DateUtilsDemo {

    public static void main(String[] args) throws ParseException {

        // 给Date添加几天 减少则第二个参数为负数
        System.out.println(DateUtils.addDays(new Date(),3));

        // 判断是否是同一天
        System.out.println(DateUtils.isSameDay(new Date(),new Date()));

        // 字符串转换Date 可以输入多个匹配模式
        System.out.println(DateUtils.parseDate("2020-02-09 20:23:32","yyyy-MM-dd HH:mm:ss","yyyy/MM/dd HH:mm:ss"));

        // 设置为本月的第几天 设置月/年/小时/分钟等同理
        System.out.println(DateUtils.setDays(new Date(),31));
        System.out.println(DateUtils.setHours(new Date(),23));
        System.out.println(DateUtils.setMonths(new Date(),9));
        System.out.println(DateUtils.setYears(new Date(),2022));

        // 计算过去了多久
        // 计算从这个月开始过去了多少天
        System.out.println(DateUtils.getFragmentInDays(new Date(), Calendar.MONDAY));
        // 计算今年开始过去了多少天
        System.out.println(DateUtils.getFragmentInDays(new Date(),Calendar.YEAR));
        // 计算今天开始过去了多少小时
        System.out.println(DateUtils.getFragmentInHours(new Date(),Calendar.DATE));
        // 计算今年开始过去了多少小时
        System.out.println(DateUtils.getFragmentInHours(new Date(),Calendar.YEAR));
        // 计算这天过去了多少分钟
        System.out.println(DateUtils.getFragmentInMinutes(new Date(),Calendar.DATE));

    }

}
```

- 反射相关工具包

```java
package com.rocks.commons.lang3;

import com.rocks.commons.lang3.vo.DemoClass2;
import com.rocks.commons.lang3.vo.MyAnnotation;
import org.apache.commons.lang3.ClassUtils;
import org.apache.commons.lang3.reflect.MethodUtils;

import java.lang.reflect.InvocationTargetException;
import java.util.Arrays;

/**
 * 增强反射相关的工具类代码示例
 * @author lizhaoxuan
 */
public class ReflectUtilsDemo {

    public static void main(String[] args) throws ClassNotFoundException, NoSuchMethodException, IllegalAccessException, InvocationTargetException {

        // 根据className获取class 可以指定是否进行初始化
        Class<?> aClass = ClassUtils.getClass("java.lang.Integer",false);

        // 判断是否是内部类
        System.out.println(ClassUtils.isInnerClass(aClass));

        // 判断是否是原始类型或基础类型的包装类
        System.out.println(ClassUtils.isPrimitiveOrWrapper(aClass));
        System.out.println(ClassUtils.isPrimitiveOrWrapper(Integer.class));

        // 获取简短Class名称
        System.out.println(ClassUtils.getShortClassName(aClass));

        // 获取包名
        System.out.println(ClassUtils.getPackageName(aClass));

        // 获取该类的所有父类 不包含接口
        System.out.println(ClassUtils.getAllSuperclasses(aClass));

        // 获取该类的所有接口
        System.out.println(ClassUtils.getAllInterfaces(aClass));

        // 将class和className相互转换
        System.out.println(ClassUtils.convertClassesToClassNames(Arrays.asList(Integer.class,Boolean.class)));
        System.out.println(ClassUtils.convertClassNamesToClasses(Arrays.asList("java.lang.Integer","java.lang.Boolean")));

        // 判断是否是相同的class  包装类型和基本类型class一致
        System.out.println(ClassUtils.isAssignable(Integer.class, int.class));

        // 获取该类的继承结构
        Iterable<Class<?>> hierarchy = ClassUtils.hierarchy(Integer.class);
        hierarchy.forEach(System.out::println);

        // ==========  MethodUtils  ==========================

        // 获取带指定注解的方法  必须是运行时注解 编译期注解无法查找 编译完成后就消失了
        // 后面两个参数是 是否搜找父类和父接口 是否忽略权限(如果为false 则不能查到private)  默认查找父类 不查找private
        System.out.println(MethodUtils.getMethodsListWithAnnotation(DemoClass2.class, MyAnnotation.class));
        System.out.println(MethodUtils.getMethodsListWithAnnotation(DemoClass2.class, MyAnnotation.class,true,true));

        // 调用Method
        System.out.println(MethodUtils.invokeMethod(new Object(),"toString",null));

        // 获取可使用的Method 非private 无须暴力反射
        System.out.println(MethodUtils.getAccessibleMethod(Object.class,"toString"));

    }

}
```

- 秒表

```java
package com.rocks.commons.lang3;

import org.apache.commons.lang3.RandomUtils;
import org.apache.commons.lang3.time.StopWatch;

/**
 * lang3包其他工具类
 * @author lizhaoxuan
 */
public class OtherUtilsDemo {

    public static void main(String[] args) throws InterruptedException {

        // 秒表
        StopWatch stopWatch = new StopWatch();
        stopWatch.start();
        Thread.sleep(RandomUtils.nextLong(3*1000,10*1000));
        stopWatch.stop();
        System.out.println(stopWatch.getTime());
        System.out.println(stopWatch.getNanoTime());

    }

}
```

